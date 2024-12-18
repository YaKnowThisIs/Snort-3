# Evidence of Execution  
**jeudi 28 novembre 2024** | **10:18**

---

## Objectif  
- Examiner les artéfacts fournissant des preuves d'exécution  

---

## Windows Prefetch files  
- Lorsqu'un programme est exécuté sous Windows, il stocke ses informations pour une utilisation ultérieure  
    - Utilisées pour charger le programme rapidement en cas d'utilisation fréquente  
- Ces informations sont stockées dans  
    ```plaintext
    C:\Windows\Prefetch
    ```
- Les fichiers de prelecture ont une extension `.pf` et contiennent :  
    - Le chemin des fichiers d'exécution de l'application  
    - Le nombre de fois que l'application a été exécutée  
    - Tous les fichiers et identifiants de périphérique utilisés par le fichier  

---

### Prefetch Parser - PECmd.exe  
Outil d'Éric Zimmerman pour analyser les fichiers Prefetch et extraire les données  

| Commande                                | Description                                                       |
|-----------------------------------------|-------------------------------------------------------------------|
| `PECmd.exe`                             | Afficher l'aide                                                  |
| `PECmd.exe -f <path-to-Prefetch-file> --csv <path-to-save-csv>` | Exécuter Prefetch Parser sur un fichier et enregistrer les résultats dans un fichier CSV |
| `PECmd.exe -d <path-to-Prefetch-directory> --csv <path-to-save-csv>` | Exécuter Prefetch Parser sur un répertoire entier et enregistrer les résultats dans un fichier CSV |

---

## Windows 10 Timeline  
- Windows 10 stocke les applications et les fichiers récemment utilisés dans une base de données SQLite appelée **chronologie Windows 10**  
- Ces données sont une source d'informations sur les derniers programmes exécutés  
    - Elles contiennent l'application qui a été exécutée et le temps de focus de l'application  
- La timeline Windows 10 se trouve à l'emplacement suivant :  
    ```plaintext
    C:\Users\<username>\AppData\Local\ConnectedDevicesPlatform\{randomfolder}\ActivitiesCache.db
    ```

---

### WxTCmd.exe  
Outil d'Éric Zimmerman pour analyser la chronologie de Windows 10  

| Commande                                  | Description                                                       |
|-------------------------------------------|-------------------------------------------------------------------|
| `WxTCmd.exe`                              | Afficher l'aide                                                  |
| `WxTCmd.exe -f <path-to-timeline-file> --csv <path-to-save-csv>` | Exécuter WxTCmd sur un fichier et enregistrer les résultats dans un fichier CSV |

---

## Windows 10 Jumplist  
- Permet aux utilisateurs d'accéder à leurs fichiers récemment utilisés depuis la barre des tâches  
- Inclue également des informations sur :  
    - Les applications exécutées  
    - La première heure d'exécution  
    - La dernière heure d'exécution de l'application par rapport à un **AppID**  
- Pour afficher les fichiers de raccourcis :  
    - **Clic droit** sur l'icône d'une application dans la barre des tâches  
    - Affiche les fichiers récemment ouverts dans cette application  
- Ces données sont stockées dans le répertoire suivant :  
    ```plaintext
    C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations
    ```

---

### JLECmd.exe  
Outil d'Éric Zimmerman pour analyser les jumplists  

| Commande                                  | Description                                                       |
|-------------------------------------------|-------------------------------------------------------------------|
| `JLECmd.exe`                              | Afficher l'aide                                                  |
| `JLECmd.exe -f <path-to-Jumplist-file> --csv <path-to-save-csv>` | Exécuter JLECmd sur un fichier et enregistrer les résultats dans un fichier CSV |

---
