## VM-Health Script


#!/bin/bash

# Azure Storage variables
AZURE_STORAGE_ACCOUNT="your-storage-account-name"
AZURE_STORAGE_CONTAINER="vm-health-monitoring"
AZURE_STORAGE_KEY="your-storage-account-key"

# List of VMs to monitor (you can replace with IPs/hostnames)
VM_LIST=("vm1" "vm2" "vm3" "vm4" "vm5" "vm6" "vm7" "vm8" "vm9" "vm10")

# Loop through each VM and gather health metrics
for VM in "${VM_LIST[@]}"; do
    echo "Checking health for $VM"

    # SSH into the VM and collect CPU, memory, and disk usage statistics
    CPU_USAGE=$(ssh user@$VM " top -bn1 | grep 'Cpu(s)' | awk '{print $2 + $4}'")
    MEM_USAGE=$(ssh user@$VM "free -m | awk 'NR==2{printf "Memory Usage: %.2f%%\n", $3*100/$2 }'")
    DISK_USAGE=$(ssh user@$VM "df -h | awk '$NF=="/"{printf "%s\n", $5}'")

    # Generate the output file with VM health data
    echo "CPU Usage: $CPU_USAGE%" > /tmp/$VM-health.txt
    echo "Memory Usage: $MEM_USAGE" >> /tmp/$VM-health.txt
    echo "Disk Usage: $DISK_USAGE" >> /tmp/$VM-health.txt

    # Upload the file to Azure Blob Storage
    az storage blob upload --account-name $AZURE_STORAGE_ACCOUNT --account-key $AZURE_STORAGE_KEY --container-name $AZURE_STORAGE_CONTAINER --file /tmp/$VM-health.txt --name "$VM-health-$(date +'%Y-%m-%d_%H-%M').txt"
done
