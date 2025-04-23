# PowerShell-Project
This is a powershell script suite designed to automate common system administration tasks such as user provisioning, patch management, software deployment, and compliance reporting. 


# PowerShell Script Suite: AdminAutomationSuite

# Module 1: User Provisioning
function New-UserProvisioning {
    param (
        [string]$UserName,
        [string]$Password,
        [string]$OU = "OU=Users,DC=example,DC=com"
    )
    Write-Host "Provisioning user: $UserName"
    New-ADUser -Name $UserName -AccountPassword (ConvertTo-SecureString $Password -AsPlainText -Force) -Path $OU -Enabled $true
    Write-Host "User $UserName provisioned successfully."
}

# Module 2: Patch Management
function Invoke-PatchManagement {
    Write-Host "Starting patch management..."
    Install-WindowsUpdate -AcceptAll -IgnoreReboot -Verbose
    Write-Host "Patch management completed."
}

# Module 3: Software Deployment
function Deploy-Software {
    param (
        [string]$SoftwarePath,
        [string]$TargetMachine
    )
    Write-Host "Deploying software from $SoftwarePath to $TargetMachine..."
    Invoke-Command -ComputerName $TargetMachine -ScriptBlock {
        Start-Process -FilePath $using:SoftwarePath -ArgumentList "/quiet /norestart" -Wait
    }
    Write-Host "Software deployed to $TargetMachine."
}

# Module 4: Compliance Reporting
function Generate-ComplianceReport {
    param (
        [string]$ReportPath = "C:\Reports\ComplianceReport.csv"
    )
    Write-Host "Generating compliance report..."
    Get-WinEvent -LogName Security | Export-Csv -Path $ReportPath -NoTypeInformation
    Write-Host "Compliance report saved at $ReportPath."
}

# Menu for the script suite
function Show-Menu {
    Write-Host "Admin Automation Suite"
    Write-Host "1. User Provisioning"
    Write-Host "2. Patch Management"
    Write-Host "3. Software Deployment"
    Write-Host "4. Compliance Reporting"
    Write-Host "5. Exit"
}

# Main Script Execution
do {
    Show-Menu
    $choice = Read-Host "Enter your choice"

    switch ($choice) {
        "1" {
            $UserName = Read-Host "Enter username"
            $Password = Read-Host "Enter password"
            New-UserProvisioning -UserName $UserName -Password $Password
        }
        "2" {
            Invoke-PatchManagement
        }
        "3" {
            $SoftwarePath = Read-Host "Enter the software path"
            $TargetMachine = Read-Host "Enter the target machine"
            Deploy-Software -SoftwarePath $SoftwarePath -TargetMachine $TargetMachine
        }
        "4" {
            $ReportPath = Read-Host "Enter the path to save the compliance report (default: C:\Reports\ComplianceReport.csv)"
            if (-not $ReportPath) { $ReportPath = "C:\Reports\ComplianceReport.csv" }
            Generate-ComplianceReport -ReportPath $ReportPath
        }
        "5" {
            Write-Host "Exiting Admin Automation Suite."
        }
        default {
            Write-Host "Invalid choice. Please try again."
        }
    }
} while ($choice -ne "5")
