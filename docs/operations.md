# Operations guide

This guide covers the two lab VMs used during the Cloudroom milestone. Run Azure CLI commands from an authenticated shell with access to the correct subscription.

## Before stopping

1. Confirm the latest documentation and required source artifacts are committed to version control.
2. Confirm no job or interactive session still needs either VM.
3. Keep all secrets out of shell transcripts, screenshots, Git commits, and diagnostic output.

## Stop compute

Deallocate the isolated Cloudroom VM first:

```bash
az vm deallocate --resource-group RG-CLOUDROOM-ISOLATED --name vm-cloudroom-core-01
```

Then deallocate the older lab VM if it is no longer needed:

```bash
az vm deallocate --resource-group RG-AI-LAB-OLLAMA --name vm-ollama-lab-01
```

Verify both states:

```bash
az vm get-instance-view --resource-group RG-CLOUDROOM-ISOLATED --name vm-cloudroom-core-01 --query "instanceView.statuses[?starts_with(code, 'PowerState/')].displayStatus" --output tsv
az vm get-instance-view --resource-group RG-AI-LAB-OLLAMA --name vm-ollama-lab-01 --query "instanceView.statuses[?starts_with(code, 'PowerState/')].displayStatus" --output tsv
```

Expected state: **VM deallocated**.

Deallocation stops VM compute billing; it does **not** delete the VM, OS/data disks, networking resources, or other separately billed services. Review the Azure cost analysis for the subscription and any model deployments before assuming all spend has stopped.

## Start the Cloudroom VM

The isolated Cloudroom VM can be started independently:

```bash
az vm start --resource-group RG-CLOUDROOM-ISOLATED --name vm-cloudroom-core-01
```

Check the service:

```bash
az vm run-command invoke --resource-group RG-CLOUDROOM-ISOLATED --name vm-cloudroom-core-01 --command-id RunShellScript --scripts 'systemctl is-active cloudroom.service'
```

Expected service output: **active**.

Then check the local readiness endpoint from within the VM:

```bash
curl --fail --silent http://127.0.0.1:9840/v1/ready
```

Expected response observed during the milestone: {"ready":true}.

If the service is not active or readiness fails, inspect systemctl status cloudroom.service and journalctl -u cloudroom.service -n 100 --no-pager. Do not paste logs publicly before reviewing them for sensitive data.

## Important dependency note

Azure AI Foundry inference was accessed using the Cloudroom VM's managed identity and a resource-scoped role assignment. The Foundry resource being located in the older VM's resource group does not, by itself, mean the older VM must be running for inference. Confirm model availability and access after restart.

## Persistence and recovery

Cloudroom local history was present on the VM under /var/lib/cloudroom/history during the milestone. This is not proof of backup or disaster recovery. Before relying on the lab for important work:

- define a backup schedule and retention policy for required local state;
- test a restore into a clean environment;
- verify history after a VM restart;
- keep environment files, access tokens, identity tokens, and token-helper secrets out of source control.
