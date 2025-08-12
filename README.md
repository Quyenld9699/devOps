## Note:

This script is designed to clean up and reduce the size of all WSL (Windows Subsystem for Linux) distributions on your system. It performs the following tasks:

1. Copy this script to a C:\ drive or any other drive where you have write permissions.
2. Run the script using PowerShell with administrative privileges.
3. It will:
    - Clean up unnecessary files and logs within each WSL distribution.
    - Trim the filesystem to reduce unused space.
    - Compact the VHDX files associated with each WSL distribution to reclaim disk space.

## File run for PowerShell

```powershell
# Compact-WSL-All.ps1
# Tự động dọn rác & giảm dung lượng cho tất cả các WSL

Write-Host ">>> Quét danh sách WSL..."
$distros = wsl --list --quiet

foreach ($distro in $distros) {
    Write-Host "==============================="
    Write-Host ">>> Đang xử lý: $distro"

    # Dọn rác bên trong WSL
    Write-Host ">>> Dọn rác trong $distro..."
    wsl -d $distro -- bash -c "
    sudo apt-get autoremove --purge -y &&
    sudo apt-get clean &&
    sudo rm -rf /var/log/* &&
    sudo rm -rf /tmp/* &&
    sudo rm -rf /var/tmp/* &&
    sudo rm -rf ~/.cache/*
    "

    # Giảm file trống
    wsl -d $distro -- bash -c "sudo fstrim -av"
}

Write-Host ">>> Dừng WSL..."
wsl --shutdown

# Tìm tất cả file .vhdx trong thư mục WSL
Write-Host ">>> Quét file VHDX..."
$vhdxFiles = Get-ChildItem "$env:LOCALAPPDATA\Packages" -Recurse -Filter "*.vhdx" -ErrorAction SilentlyContinue

foreach ($file in $vhdxFiles) {
    Write-Host "-------------------------------"
    Write-Host "Xử lý file: $($file.FullName)"

    # Bỏ nén
    compact /u "$($file.FullName)"

    # Bỏ mã hóa nếu có
    cipher /d "$($file.FullName)"

    # Compact VHDX
    $diskpartScript = @"
select vdisk file="$($file.FullName)"
attach vdisk readonly
compact vdisk
detach vdisk
exit
"@
    $diskpartScript | diskpart
}

Write-Host ">>> Hoàn tất! Tất cả WSL đã được tối ưu."
```
