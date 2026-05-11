# 1. Vérification que le script tourne en Admin
if (-not ([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
    Write-Host "ERREUR : Ce script doit être exécuté en tant qu'Administrateur." -ForegroundColor Red
    exit
}

# 2. Détection automatique de l'installation VS 2022
$baseDir = "C:\Program Files\Microsoft Visual Studio\2022"
$editions = @("Community", "Professional", "Enterprise")
$vsBase = ""

foreach ($e in $editions) {
    if (Test-Path "$baseDir\$e") {
        $vsBase = "$baseDir\$e"
        Write-Host "[INFO] Version détectée : Visual Studio 2022 $e" -ForegroundColor Cyan
        break
    }
}

if (-not $vsBase) {
    Write-Host "[ERREUR] Visual Studio 2022 n'a pas été trouvé dans le chemin par défaut." -ForegroundColor Red
    exit
}

# 3. Cibles étendues
$targets = @(
    "$vsBase\Common7\IDE\devenv.exe",
    "$vsBase\Common7\IDE\VSIXInstaller.exe",
    "$vsBase\MSBuild\Current\Bin\MSBuild.exe", # Utile pour les outils de build
    "C:\Program Files (x86)\Microsoft Visual Studio\Installer\setup.exe"
)

$registryPath = "HKCU:\Software\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\Layers"

# 4. Application des flags
if (-not (Test-Path $registryPath)) {
    New-Item -Path $registryPath -Force | Out-Null
}

foreach ($exe in $targets) {
    if (Test-Path $exe) {
        try {
            # On utilise Set-ItemProperty qui est plus polyvalent pour la mise à jour
            Set-ItemProperty -Path $registryPath -Name $exe -Value "~ RUNASADMIN" -Force | Out-Null
            Write-Host "[OK] Mode Admin activé : $exe" -ForegroundColor Green
        }
        catch {
            Write-Host "[ERREUR] Échec pour : $exe" -ForegroundColor Red
        }
    } else {
        Write-Host "[INFO] Ignoré (non installé) : $exe" -ForegroundColor Gray
    }
}

Write-Host "`nTerminé ! Visual Studio se lancera désormais en mode Administrateur." -ForegroundColor White -BackgroundColor DarkGreen
