---
description: Disguise WINDOWS 11 - HYPERV
title: Disguise WINDOWS 11 - HYPERV
---

---
<p>Enter the machine name below. Edit the configuration table to configure your network settings. Click "Export PowerShell Script" to save the configuration to a PowerShell script file.</p>

This Tool is designed to create a HYPERV network switch defined by the name of each unique adaptor in a server.
For example, a Hyper-V switch will be created called - MELLANOX CX6.

Each VLAN will then be tagged onto this switch per adapter.

To remove the adapter, user can execute `REMOVE-VMSWITCH-MELLANOX CX6`.

<table id="machineNameTable">
  <thead>
    <tr>
      <th>NAME OF MACHINE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td contenteditable="true" id="machineNameCell">Enter Machine Name</td>
    </tr>
  </tbody>
</table>

<table id="networkConfigTable">
  <thead>
    <tr>
      <th>VLAN Name</th>
      <th>Disguise Hardware Configuration</th>
      <th>Adapter Name</th>
      <th>VLAN</th>
      <th>IP Address</th>
      <th>Actions</th>
    </tr>
  </thead>
  <tbody>
    <!-- Rows will be dynamically populated -->
  </tbody>
</table>

<button class="btn" onclick="addRow()">Add Row</button>
<button class="btn" onclick="exportPowerShell()">Export PowerShell Script</button>
<button class="add-row-btn" onclick="exportRemoveVMSwitch()">Remove VMSwitch</button>

<style>
  /* Style fix for dropdown and table */
  input[list] {
    width: 100%;
    box-sizing: border-box;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 20px;
  }

  th, td {
    border: 1px solid #ccc;
    padding: 8px;
    text-align: left;
  }

  th {
    background-color: #22445c
  }

  td select, td input {
    width: 100%;
  }
</style>

<script>
  // Initial Network Configuration for first 4 rows (Make sure VLAN values are numbers, not strings)
const networkConfig = [
    { VLANName: "d3Net", DisguiseConfig: "A - 10Gbit", AdapterName: "Intel(R) I210 Gigabit Network Connection", VLAN: 10, IP: "" },
    { VLANName: "ARTNET", DisguiseConfig: "B - 10Gbit", AdapterName: "Intel(R) I210 Gigabit Network Connection", VLAN: 20, IP: "" },
    { VLANName: "Media Net", DisguiseConfig: "C - 100Gbit", AdapterName: "Intel(R) I210 Gigabit Network Connection", VLAN: 30, IP: "" },
    { VLANName: "MGMT", DisguiseConfig: "D - 100Gbit", AdapterName: "Intel(R) I210 Gigabit Network Connection", VLAN: 40, IP: "" }
];

// Adapter options for dropdown (alphabetized)
const adapterOptions = [
    "Intel(R) Ethernet Controller X710 for 10GBASE-T",
    "Intel(R) Ethernet Controller X710 for 10GBASE-T #2",
    "Intel(R) Ethernet Controller X722 for 10GBASE-T #1",
    "Intel(R) Ethernet Controller X722 for 10GBASE-T #2",
    "Intel(R) I210 Gigabit Network Connection",
    "Intel(R) I210 Gigabit Network Connection #2",
    "Intel(R) I210 Gigabit Network Connection #3",
    "Intel(R) I210 Gigabit Network Connection #4",
    "Mellanox ConnectX-6 Dx Adapter",
    "Mellanox ConnectX-6 Dx Adapter #2",
    "Mellanox ConnectX-5 Dx Adapter",
  "Mellanox ConnectX-5 Dx Adapter #2",
].sort();

// Disguise hardware configurations (alphabetized)
const disguiseOptions = [
    "10Gbit - 1",
    "10Gbit - 2",
    "1Gbit - 3",
    "1Gbit - 4",
    "A - 10Gbit",
    "A - d3Net 1Gbit",
    "B - 10Gbit",
    "B - Media 10Gbit",
    "C - 100Gbit",
    "C - Media 10Gbit",
    "D - 100Gbit",
    "D - 25Gbit",
    "E - 100Gbit",
    "E - 25Gbit",
    "F - 100Gbit"
].sort();

// Popular VLAN names
const vlanNameOptions = [
    "d3Net",
    "KVM Net",
    "Media Net",
    "Automation Net (PSN)",
    "NDI Net",
    "Internet",
    "sACN/Artnet",
    "OSC/Control",
    "Omnical",
    "PTZ Camera Control",
    "MGMT"
].sort();

// Populate the configuration table with initial data
function populateTable() {
    const tableBody = document.getElementById('networkConfigTable').querySelector('tbody');
    tableBody.innerHTML = ''; // Clear existing rows
    networkConfig.forEach((config, index) => {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td>
                <input list="vlanNames" value="${config.VLANName}" oninput="updateConfig(${index}, 'VLANName', this.value)" />
                <datalist id="vlanNames">
                    ${vlanNameOptions.map(option => `<option value="${option}"></option>`).join('')}
                </datalist>
            </td>
            <td>
                <select onchange="updateConfig(${index}, 'DisguiseConfig', this.value)">
                    ${disguiseOptions.map(option => 
                        `<option value="${option}" ${option === config.DisguiseConfig ? 'selected' : ''}>${option}</option>`
                    ).join('')}
                </select>
            </td>
            <td>
                <select onchange="updateConfig(${index}, 'AdapterName', this.value)">
                    ${adapterOptions.map(adapter => 
                        `<option value="${adapter}" ${adapter === config.AdapterName ? 'selected' : ''}>${adapter}</option>`
                    ).join('')}
                </select>
            </td>
            <td contenteditable="true" oninput="updateConfig(${index}, 'VLAN', parseInt(this.textContent.trim()) || '')">${config.VLAN}</td>
            <td contenteditable="true" oninput="updateConfig(${index}, 'IP', this.textContent.trim())">${config.IP}</td>
            <td><button class="btn" onclick="deleteRow(${index})">Delete</button></td>
        `;
        tableBody.appendChild(row);
    });
}

// Update configuration array when a change is made in the table
function updateConfig(index, key, value) {
    networkConfig[index][key] = value;
}

// Add a new row with default configuration
function addRow() {
    const lastRow = networkConfig[networkConfig.length - 1] || {};
    const lastVLAN = lastRow.VLAN || 0;

    // Increment VLAN by 10
    const newVLAN = lastVLAN + 10;

    networkConfig.push({ 
        VLANName: "", 
        DisguiseConfig: lastRow.DisguiseConfig || disguiseOptions[0], 
        AdapterName: lastRow.AdapterName || adapterOptions[0], 
        VLAN: newVLAN, 
        IP: "" 
    });
    populateTable();
}


// Delete a row from the table
function deleteRow(index) {
    if (confirm("Are you sure you want to delete this row?")) {
        networkConfig.splice(index, 1);
        populateTable();
}

// Export configuration as PowerShell Script
function exportPowerShell() {
    const machineName = document.getElementById('machineNameCell').textContent.trim();
    if (!machineName || machineName === "Enter Machine Name") {
        alert("Please enter a valid machine name!");
        return;
    }

    let psScript = `# ${machineName} - Disguise HYPERV Config\n\n`;

    // Set to track the switches already added
    const switchesAdded = new Set();

    networkConfig.forEach(config => {
        if (config.AdapterName && config.DisguiseConfig && config.VLANName) {
            // The Switch Name is based on the Adapter Name
            const switchName = config.AdapterName;
            // The Net Adapter Name is based on the Disguise Hardware Configuration
            const netAdapterName = config.DisguiseConfig;

            // Add the New-VMSwitch command only if the switch hasn't been added yet
            if (!switchesAdded.has(switchName)) {
                psScript += `New-VMSwitch -Name "${switchName}" -AllowManagementOS $true -NetAdapterName "${netAdapterName}"\n`;
                switchesAdded.add(switchName); // Mark this switch as added
            }

            // Create VM Network Adapter using the VLAN Name
            psScript += `Add-VMNetworkAdapter -ManagementOS -Name "${config.VLANName}" -SwitchName "${switchName}"\n`;

            // Set VLAN ID using the VLAN Name
            psScript += `Set-VMNetworkAdapterVlan -VMNetworkAdapterName "${config.VLANName}" -VlanId ${config.VLAN || 0} -Access -ManagementOS\n`;

            if (config.IP) {
                psScript += `# Assign IP Address: ${config.IP} to VLAN ${config.VLAN}\n`;
            }
        }
    });

    // Create and download the PowerShell script with dynamic file name
    const blob = new Blob([psScript], { type: "text/plain" });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = `${machineName}-NETWORK-CONFIG.ps1`;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
}


// Function to generate the PowerShell script for removing VMSwitch for each NIC
function exportRemoveVMSwitch() {
    // Get all unique adapter names in the networkConfig array
    const uniqueAdapters = [...new Set(networkConfig.map(config => config.AdapterName))];

    // Start the PowerShell script
    let psScript = '# PowerShell script to remove VMSwitch for each NIC\n\n';

    // Loop through each unique adapter name and add the Remove-VMSwitch command
    uniqueAdapters.forEach(adapter => {
        psScript += `Remove-VMSwitch -Name "${adapter}" -Force\n`;
    });

    // Create and download the PowerShell script with dynamic file name
    const blob = new Blob([psScript], { type: "text/plain" });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = "REMOVE-VMSWITCH.ps1"; // Download as a fixed name file
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
}

window.onload = function() {
    populateTable();  // This will populate the table when the page is loaded or refreshed
};
</script>

<details>
With Special Thanks to ,
:Spencer Pokorski, for a hint with the IP ranges
:Joe Theobold
</Details>