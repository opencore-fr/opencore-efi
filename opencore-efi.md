# Ajout des fichiers principaux OpenCore

Pour créer la structure du dossier OpenCore, vous allez devoir prendre le dossier trouvable dans les [versions finales de OpenCorePkg](https://github.com/acidanthera/OpenCorePkg/releases/). Prenez note qu'ils seront sous les dossiers IA32 ou X64, le 1er pour les systèmes 32-bit et le 2èpour les systèmes 64-bit.:

![](https://dortania.github.io/OpenCore-Install-Guide/assets/img/ia32-x64.aa5dccd9.png)

Debug VS Release :

* **DEBUG**: Peux beaucoup aider pour débugger les problèmes de boot (démarrage), mais ajoute un peu de temps à chaque démarrages (genre 3 à 5 secondes pour aller aux sélecteur de boot) Une fois installé, vous pouvez facilement passer à une version release
* **RELEASE**: Temps de démarrages raccourcis, mais n'as pas de débug facilement, ce qui rend la résolution de problèmes bien plus difficiles.

Une fois téléchargé, mettez le dossier EFI (de OpenCorePkg) à la racine de votre partition EFI:

![](https://dortania.github.io/OpenCore-Install-Guide/assets/img/efi-moved.87262fda.png)

**Note**:

* **Utilisateurs Windows:** Mettez le dossier à la racine de la clé USB crée plus tot
* **Linux users:** C'est la partition `OPENCORE` crée plus tot.
  * Notre que la méthode 1 crée 1 partition, alors que la méthode 2 crée 2 partitions.

Maintenant ouvrons le dossier EFI et regardons ce qu'il y a dedans

![dosser EFI de base ](https://dortania.github.io/OpenCore-Install-Guide/assets/img/base-efi.7500e22d.png)

Quelque chose que vous avez du remarquer maintenant est que le dossier `Drivers` et  `Tools` contienne beaucoup de choses inutiles dont on a pas besoin

* **Gardez les fichiers suivants dans le dossier `Drivers`**(si possible):

| Pilote | Nécessaire ou non | Description |
| :--- | :--- | :--- |
| OpenUsbKbDxe.efi | <span style="color:#30BCD5"> Optionnel </span> | Nécessaire pour les PC non-UEFI (avant 2012) |
| OpenPartitionDxe.efi | ^^ | Nécessaire pour lancer la partition de récupération de macOS 10.7 à macOS 10.9  |
| OpenRuntime.efi | <span style="color:red"> Nécessaire </span> | Nécessaire pour que ça fonctionne bien|

<details> 
 <summary>Plus d'infos sur les pilotes</summary>

* AudioDxe.efi
  * 0 rapport pour le support audio dans macOS
* CrScreenshotDxe.efi
  * Utilisé pour prendre des captures d'écrans sur les systèmes UEFI, mais on en a pas besoin
* HiiDatabase.efi
  * Utilisé pour réparer la prise en charge de l'interface graphique comme OpenShell.efi sur Sandy Bridge et les versions antérieures
  * Non nécrssaire pour démarrer
* NvmExpressDxe.efi
  * Utilisé pour Haswell et versions antérieures lorsqu'aucun pilote NVMe n'est intégré au micrologiciel
  * Ne pas utiliser SAUF si vous savez pertinemment ce que vous faites
* OpenCanopy.efi
  * L'interface graphique optionnelle d'OpenCore, on verra comment la configurer dans [l'après insallation](https://dortania.github.io/OpenCore-Post-Install/cosmetic/gui.html) donc retirez le pour le moment
* OpenHfsPlus.efi
  * Pilote HFSPlus opensource, assez lent, on vous recommande donc de ne pas l'utiliser à moins que vous savez ce que vous faites.
* OpenPartitionDxe.efi
  * Nécessaire pour lancer la partition de récupération de macOS 10.7 à macOS 10.9 
    * Note: Utilisateurs d'OpenDuet (donc sans UEFI) auront ce pilote déjà intégré, donc pas besoin
* OpenUsbKbDxe.efi
  * Utilisé pour le sélecteur OpenCore sur **les anciens systèmes exécutant DuetPkg**, [non  recommandé et très nuisible sur IvyBridge et plus récent](https://applelife.ru/threads/opencore-obsuzhdenie-i-ustanovka.2944066/page-176#post-856653)
* Ps2KeyboardDxe.efi + Ps2MouseDxe.efi
  * Assez bizarre si vous en avez besoin, ceux avez souris USB et clavier USB en ont pas besoin
  * Rappelez vous: PS2 ≠ USB
* UsbMouseDxe.efi
  * Un peu comme OpenUsbKbDxe, uniquement nécessaire sur les anciens systèmes utilisant DuetPkg
* XhciDxe.efi
  * utilisé pour Sandy Bridge et plus vieux quand aucun pilotes XHCI sont déjà inclus dans le logiciel
  * Uniquement nécessaire si vous utilisez une carte d'expension USB 3.0 dans une plus vielle machine

  </details>

* **Gardes les fichiers suivants dans `Tools`** 

| Outil | nécessaire ou non | Description |
| :--- | :--- | :--- |
| OpenShell.efi | <span style="color:#30BCD5"> Optionel </span> | Recommandé pour un débuggage plus facile |

Un EFI Propre :

![EFI propre](https://dortania.github.io/OpenCore-Install-Guide/assets/img/clean-efi.10fb2a26.png)

mainetant vous pouvez mettre **vos** pilotes logiciels nécessaires(.efi) dans le dossier _Drivers_ et Kexts/ACPI dans leurs dossier respectifs. Voir [Collecte de Fichiers](https://dortania.github.io/OpenCore-Install-Guide/ktext.html#firmware-drivers) pour plus d'infos sur quels fichiers utiliser.

* PRENEZ EN CONTE QUE LES DRIVERS CLOVER NE MARCHENET PAS SUR OPENCORE !!!!!(EmuVariableUEFI, AptioMemoryFix, OsxAptioFixDrv, etc). merci de voir le [guide de conversion de logiciel clover](https://github.com/dortania/OpenCore-Install-Guide/blob/master/clover-conversion/clover-efi.md) pour plus d'infos sur les pilotes supportées et ceux fussionés avec opencore.

Voici a quoi **_peux_** ressembler un EFI 'peuplé' (le votre peux être différent):

![Dossier EFI Peuplé](https://dortania.github.io/OpenCore-Install-Guide/assets/img/populated-efi.8d46cc52.png)

**Souvenez-vous**:

* les SSDTs et les custom DSDTs(`.aml`) vont dans le dossier `ACPI`
* Les Kexts(`.kext`) vont dans le dossier `Kexts`
* les pilotes de logiciel (`.efi`) vont dans le dosier `Drivers`

# Maintenant que tout ça est fait, direction  [Collecte de fichiers ](https://dortania.github.io/OpenCore-Install-Guide/ktext.html#firmware-drivers) pour récupérer les fichiers Kexts et les pilotes nécessaires.
