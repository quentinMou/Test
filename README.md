# Pré-requis — Lab « Outillage Agentique de Copilot »

Hypothèse : les participants utilisent Windows.

Ce lab utilise Visual Studio Code et non Visual Studio.

Ce document liste les prérequis pour réaliser le lab, avec pour chaque:

1. comment vérifier s'il est installé ;
2. comment l'installer si nécessaire.

---

## Outils d'installation Windows

Ces outils, en particulier `Winget`, sont listés en premier car ils permettent ensuite d'installer une grande partie des autres prérequis.

### winget (Windows Package Manager)
- Rôle : gestionnaire de paquets Windows recommandé pour installer les autres outils.
- Vérifier :

```powershell
winget --version
```

- Installer :
  - Sur Windows 10/11, `winget` est généralement fourni par `App Installer`.
  - S'il est absent, installer ou mettre à jour `App Installer` depuis le Microsoft Store.
  - En alternative, suivre la documentation Microsoft : https://aka.ms/getwinget

### Chocolatey (`choco`) - optionnel et non strictement requis pour le lab mais peut-être utile en général
- Rôle : autre gestionnaire de paquets Windows, utile si vous l'utilisez déjà dans votre environnement.
- Vérifier :

```powershell
choco --version
```

- Installer : suivre la documentation officielle https://chocolatey.org/install.
- Remarque : pour ce lab, `winget` reste l'outil d'installation préféré.

---

## Outils indispensables

### Visual Studio Code
- Vérifier :

  ```powershell
  code --version
  ```

- Si la commande `code` n'est pas reconnue, vérifier aussi visuellement que Visual Studio Code est installé dans le menu Démarrer.
- Installer :
  - Avec `winget` : `winget install --id Microsoft.VisualStudioCode --source winget`
  - Ou via le site officiel : https://code.visualstudio.com/
  - Si VS Code est déjà installé mais que `code` n'est pas disponible dans le terminal :
    - Sur macOS / Linux : ouvrir VS Code puis exécuter `Ctrl+Shift+P` -> `Shell Command: Install 'code' command in PATH`.
    - Sur Windows : suivre la procédure ci‑dessous pour ajouter `code` au `PATH`.

  - Ajouter `code` au `PATH` (Windows) :
    1. Identifier le dossier `bin` de VS Code (exemples) :
       - Installateur système : `C:\Program Files\Microsoft VS Code\bin`
       - Installateur utilisateur : `%LOCALAPPDATA%\Programs\Microsoft VS Code\bin`

    2. Vérifier l'existence (PowerShell) :

        ```powershell
        Test-Path "$env:ProgramFiles\Microsoft VS Code\bin\code.cmd"
        Test-Path "$env:LOCALAPPDATA\Programs\Microsoft VS Code\bin\code.cmd"
        ```

    3. Option GUI (recommandée) :
         - Ouvrir le menu Démarrer → rechercher « Modifier les variables d'environnement » → `Modifier les variables d'environnement pour votre compte`.
         - Cliquer sur `Variables d'environnement...` → sous `Variables utilisateur`, sélectionner `Path` → `Modifier` → `Nouveau`.
         - Coller le chemin du dossier `bin` (ex. `C:\Users\<votre-nom>\AppData\Local\Programs\Microsoft VS Code\bin`) → `OK`.
         - Fermer et rouvrir votre terminal (ou redémarrer la session) puis vérifier `code --version`.
    4. Option PowerShell (persiste pour l'utilisateur) — méthode recommandée :

        ```powershell
        $codePath = Join-Path $env:LOCALAPPDATA 'Programs\Microsoft VS Code\bin'
        $current = [Environment]::GetEnvironmentVariable('Path', 'User')
        if ($current -notlike "*$codePath*") {
          [Environment]::SetEnvironmentVariable('Path', "$current;$codePath", 'User')
          Write-Host "Chemin ajouté à la variable Path utilisateur. Ouvrez un nouveau terminal."
        } else {
          Write-Host "Le chemin existe déjà dans la variable Path utilisateur."
        }
        ```

    - Alternative rapide (moins recommandée — peut tronquer `PATH`) :

      ```powershell
      $codePath = "$env:LOCALAPPDATA\Programs\Microsoft VS Code\bin"
      setx PATH "$($env:PATH);$codePath"
      ```

    5. Vérifier :

        ```powershell
        code --version
        ```

Remarques :
- Après modification du `PATH`, ouvrez un nouveau terminal (les changements ne s'appliquent pas aux sessions déjà ouvertes).
- Si vous avez installé VS Code avec l'installateur système, il est souvent plus simple de réinstaller en cochant l'option « Add to PATH ».
- Modifier la variable `Path` système nécessite des droits administrateur ; la méthode ci‑dessus modifie la variable `Path` de l'utilisateur (suffisante pour un usage local).

### PowerShell dernière version (PowerShell 7+)
- Vérifier :

```powershell
pwsh --version
```

- Vérification complémentaire depuis Windows PowerShell :

```powershell
$PSVersionTable.PSVersion
```

- Installer :
  - Avec `winget` : `winget install --id Microsoft.PowerShell --source winget`
  - Ou via la page officielle : https://github.com/PowerShell/PowerShell

### npm / npx
- Vérifier :

```powershell
npm -v
npx -v
```

- Installer :
  - `npm` et `npx` sont installés avec Node.js.
  - Avec `winget` : `winget install --id OpenJS.NodeJS.LTS --source winget`
  - Ou via le site officiel : https://nodejs.org/

### Azure CLI (`az`)
- Vérifier :

```powershell
az --version
```

- Installer :
  - Avec `winget` : `winget install --id Microsoft.AzureCLI --source winget`
  - Ou via la documentation officielle : https://learn.microsoft.com/cli/azure/install-azure-cli-windows

### Extension Azure DevOps pour Azure CLI
- Vérifier :

```powershell
az extension show --name azure-devops
```

- Installer :

```powershell
az extension add --name azure-devops
```

- Mettre à jour si nécessaire :

```powershell
az extension update --name azure-devops
```

### Github Copilot CLI
GitHub Copilot CLI est un outil en ligne de commande qui expose les capacités de Copilot dans le terminal : génération et complétion de code, chat interactif, aides pour PRs et snippets. Nécessite un compte GitHub avec abonnement Copilot et s'authentifie via le flux GitHub.

Vérifier / installer (Windows) :

````bash
# vérifier
copilot --version

# installer (winget)
winget install GitHub.Copilot

# ou via npm
npm install -g @github/copilot

# lancer et authentifier
copilot
# puis saisir /login dans l'interface interactive
````

- Pré-requis : une souscription GitHub Copilot active et, sous Windows, PowerShell 6 ou plus récent.

- Puis saisir la commande `/login` dans l'interface interactive et suivre les instructions affichées.

>**Remarque** : l'ancienne extension Copilot pour GitHub CLI (`gh`) est retirée et remplacée par GitHub Copilot CLI.

### `uv` (Astral)
uv est le client en ligne de commande d’Astral permettant de créer, lancer et gérer des environnements de développement cloud (workspaces), de synchroniser/exécuter du code à distance, forwarder des ports et ouvrir des sessions interactives pour le développement distant.

Il n'est pas strictement indispensable pour le lab partie 1 (outillage), mais le sera pour la partie 2 (SDD).
- Vérifier :

```powershell
uv --version
```

- Installer :
  - Avec `winget` : `winget install --id Astral-sh.uv --source winget`
  - Ou via l'installateur officiel Astral : https://docs.astral.sh/uv/

### .NET 10 SDK
- Vérifier :

```powershell
dotnet --list-sdks
dotnet --version
```

- Attendu : présence d'un SDK en version majeure `10`.
- Installer :
  - Avec `winget` : `winget install --id Microsoft.DotNet.SDK.10 --source winget`
  - Ou via la page officielle : https://dotnet.microsoft.com/download/dotnet/10.0
---

## Outils optionnels mais utiles

### Python
- Vérifier :

```powershell
python --version
py --version
```

- Installer :
  - Avec `winget` : `winget install --id Python.Python.3.13 --source winget`
  - Ou via le site officiel : https://www.python.org/downloads/
  - Si l'option est proposée pendant l'installation, activer `Add Python to PATH`.

### GitHub CLI
- Vérifier :

```powershell
gh --version
```

- Installer :
  - Avec `winget` : `winget install --id GitHub.cli --source winget`
  - Ou via le site officiel : https://cli.github.com/

- Authentification si nécessaire :

```powershell
gh auth login
```

### Mermaid CLI
Mermaid CLI (mmdc) est un outil en ligne de commande qui transforme des diagrammes écrits en syntaxe Mermaid en images (SVG, PNG, PDF, etc.). Il accepte des fichiers ou l'entrée standard, propose des options de thème, taille et fond, et peut être utilisé localement via npm ou ponctuellement via npx.

````bash
npx @mermaid-js/mermaid-cli -h
npm install -g @mermaid-js/mermaid-cli
mmdc -i diagram.mmd -o diagram.svg
````

- Vérifier :

```powershell
mmdc --version
```

- Vérification sans installation globale :

```powershell
npx @mermaid-js/mermaid-cli -h
```

- Installer :

```powershell
npm install -g @mermaid-js/mermaid-cli
```

- Remarque : si vous ne voulez pas l'installer globalement, `npx` peut suffire pour un usage ponctuel.

### Extension "Markdown viewer" (Chrome / Edge) — optionnelle
- Rôle : afficher rapidement des fichiers Markdown (`.md`) dans le navigateur, utile pour prévisualiser de la documentation sans ouvrir un éditeur.
- Vérifier : ouvrir un fichier `.md` local dans Chrome/Edge (glisser-déposer) — si le navigateur affiche le contenu brut, installez l'extension.
- Installer : rechercher « Markdown viewer » ou « Markdown Preview » dans le Chrome Web Store ou Microsoft Edge Add-ons et installer l'extension souhaitée.
- Autoriser l'accès aux fichiers locaux : après installation, ouvrir la page des extensions (`chrome://extensions` ou `edge://extensions`), trouver l'extension puis activer « Autoriser l'accès aux fichiers URL » (ou option équivalente) pour pouvoir prévisualiser des fichiers locaux.
- Vérifier après installation : glisser-déposer un fichier `README.md` dans une fenêtre Chrome/Edge — le rendu doit s'afficher comme dans un visualiseur Markdown.

### pandoc
Pandoc est un convertisseur universel de documents en ligne de commande qui transforme des fichiers entre de nombreux formats (Markdown, HTML, LaTeX, Word .docx, PDF via LaTeX, EPUB, etc.). Il supporte métadonnées, templates, filtres et gestion de bibliographies (citeproc) et est couramment utilisé pour automatiser la génération de rapports, slides et publications.

- Vérifier :

```powershell
pandoc --version
```

- Installer :
  - Avec `winget` : `winget install --id JohnMacFarlane.Pandoc --source winget`
  - Ou via la page officielle : https://pandoc.org/installing.html

---

## Extensions VS Code indispensables
Ci-dessous se trouve la liste des extensions VS Code indispensables pour développer des application .NET.

Vérifier que vous pouvez utiliser la commande `code` dans le terminal.
---
### `ms-dotnettools.csharp@2.130.5` - C#
- Vérifier :

```powershell
code --list-extensions --show-versions | findstr ms-dotnettools.csharp
```

- Installer :

```powershell
code --install-extension ms-dotnettools.csharp
```

- Pour installer exactement la version `2.130.5`, récupérer le fichier `.vsix` correspondant depuis l'historique Marketplace, puis l'installer :

```powershell
code --install-extension .\ms-dotnettools.csharp-2.130.5.vsix
```
---

### `ms-dotnettools.vscode-dotnet-pack@1.0.13` - .NET Extension Pack
- Vérifier :

```powershell
code --list-extensions --show-versions | findstr ms-dotnettools.vscode-dotnet-pack
```

- Installer :

```powershell
code --install-extension ms-dotnettools.vscode-dotnet-pack
```

- Pour figer exactement la version `1.0.13`, installer le `.vsix` correspondant.
---
### `ms-dotnettools.vscode-dotnet-runtime@3.0.0` - .NET Install Tool
- Vérifier :

```powershell
code --list-extensions --show-versions | findstr ms-dotnettools.vscode-dotnet-runtime
```

- Installer :

```powershell
code --install-extension ms-dotnettools.vscode-dotnet-runtime
```

- Pour figer exactement la version `3.0.0`, installer le `.vsix` correspondant.
---
### `ms-dotnettools.csdevkit@3.10.14` - C# Dev Kit
- Vérifier :

```powershell
code --list-extensions --show-versions | findstr ms-dotnettools.csdevkit
```

- Installer :

```powershell
code --install-extension ms-dotnettools.csdevkit
```

- Pour figer exactement la version `3.10.14`, installer le `.vsix` correspondant.
---
### `ms-vscode.powershell@2025.4.0` - PowerShell
- Vérifier :

```powershell
code --list-extensions --show-versions | findstr ms-vscode.powershell
```

- Installer :

```powershell
code --install-extension ms-vscode.powershell
```

- Pour figer exactement la version `2025.4.0`, installer le `.vsix` correspondant.

---

## Extensions VS Code optionnelles
Les extensions ci-dessous ne sont pas strictement nécessaire pour le lab mais sont généralement utiles pour les modèles s'ils veulent générer des scripts à la volée. Il n'est pas exclu que ce soit nécessaire durant les exercices, sans que l'on puisse le garantir.

### `ms-python.debugpy@2025.18.0` - Python Debugger
- Vérifier :

```powershell
code --list-extensions --show-versions | findstr ms-python.debugpy
```

- Installer :

```powershell
code --install-extension ms-python.debugpy
```

- Pour figer exactement la version `2025.18.0`, installer le `.vsix` correspondant.

### `ms-python.python@2026.4.0` - Python
- Vérifier :

```powershell
code --list-extensions --show-versions | findstr ms-python.python
```

- Installer :

```powershell
code --install-extension ms-python.python
```

- Pour figer exactement la version `2026.4.0`, installer le `.vsix` correspondant.

### `ms-python.vscode-pylance@2026.2.1` - Pylance
- Vérifier :

```powershell
code --list-extensions --show-versions | findstr ms-python.vscode-pylance
```

- Installer :

```powershell
code --install-extension ms-python.vscode-pylance
```

- Pour figer exactement la version `2026.2.1`, installer le `.vsix` correspondant.

### `ms-python.vscode-python-envs@1.26.0` - Python Environments
- Vérifier :

```powershell
code --list-extensions --show-versions | findstr ms-python.vscode-python-envs
```

- Installer :

```powershell
code --install-extension ms-python.vscode-python-envs
```

- Pour figer exactement la version `1.26.0`, installer le `.vsix` correspondant.

---

## Recommandations pratiques

- Ouvrir PowerShell en administrateur si une installation système échoue.
- Fermer et rouvrir le terminal après chaque installation pour recharger le `PATH`.
- Redémarrer VS Code après l'installation d'extensions ou de SDK si une détection ne se fait pas immédiatement.
- Si votre poste est contraint par un proxy ou par des politiques d'entreprise, privilégier les installateurs officiels ou les fichiers `.vsix` téléchargés à l'avance.

