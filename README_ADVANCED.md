# ESP32 Advanced Security Platform v4.0 - Red Team Edition

Ce projet est une mise à niveau majeure de la plateforme de sécurité ESP32 existante, transformant l'appareil en un outil de pentesting WiFi avancé, idéal pour les exercices universitaires **Red Team vs Blue Team**.

L'architecture a été étendue pour inclure des capacités d'attaque avancées et un scanner de vulnérabilités, le tout contrôlé via une interface web moderne et réactive.

## 1. Nouvelles Fonctionnalités de la v4.0 (Red Team)

Cette version introduit quatre modules principaux d'attaque et d'analyse, permettant une simulation réaliste des menaces sans fil.

| Module | Description | Objectif Red Team | Objectif Blue Team |
| :--- | :--- | :--- | :--- |
| **Deauth Attack Advanced** | Envoi de trames de déauthentification ciblées (unicast) ou de diffusion (broadcast) pour déconnecter les clients d'un point d'accès. | Tester la résilience des clients et des points d'accès aux attaques de déni de service (DoS) sans fil. | Détecter les inondations de trames de gestion et mettre en place des contre-mesures (802.11w PMF). |
| **Frame Fuzzer** | Génération de trames 802.11 malformées (Beacon, Probe Request, Auth, etc.) avec un taux de mutation configurable. | Découvrir des vulnérabilités de type *buffer overflow* ou des comportements inattendus dans les implémentations de piles WiFi des routeurs. | Mettre en œuvre une validation stricte des trames 802.11 reçues et surveiller les redémarrages inopinés des routeurs. |
| **WPA3 Handshake Capture** | Mise en mode promiscuité pour capturer les échanges de clés WPA3-SAE et les handshakes EAPOL. | Récupérer les données nécessaires pour des attaques de force brute hors ligne (dictionnaire) contre les mots de passe WPA/WPA2/WPA3. | S'assurer que les mots de passe sont longs et complexes, et que les mécanismes de protection contre les attaques hors ligne (comme le *Dragonfly Handshake* du WPA3) sont activés. |
| **Router Vulnerability Scanner** | Analyse passive des réseaux WiFi pour identifier les faiblesses courantes (chiffrement faible, WPS activé, SSID caché, etc.) et évaluation d'un niveau de risque. | Identifier rapidement les cibles les plus faciles à compromettre dans l'environnement Blue Team. | Utiliser les résultats du scanner pour durcir la configuration de leurs points d'accès (Blue Team). |

## 2. Architecture Technique

Le projet utilise l'architecture moderne FreeRTOS/ESP-IDF pour garantir la stabilité et la performance.

*   **Backend (C++/FreeRTOS):** Les modules d'attaque sont implémentés comme des tâches FreeRTOS distinctes, permettant une exécution concurrente et non bloquante. La communication avec l'interface web se fait via **WebSocket** et le format **JSON** pour une robustesse accrue.
*   **Frontend (HTML/Alpine.js):** L'interface utilisateur est entièrement basée sur le web (`index_advanced.html`, `app.js`, `style.css`), offrant un contrôle en temps réel sans nécessiter d'application mobile ou de bureau.

## 3. Guide d'Installation et d'Utilisation

Ce projet est configuré pour **PlatformIO**.

### 3.1. Prérequis

*   Un module ESP32 (ESP32-S3 est recommandé pour de meilleures performances WiFi).
*   VSCode avec l'extension PlatformIO.
*   Le framework ESP-IDF (géré automatiquement par PlatformIO).

### 3.2. Étapes de Construction et de Téléchargement

1.  **Copier les fichiers:** Assurez-vous que tous les fichiers du projet (y compris les nouveaux modules `.h` et `.cpp` et l'interface web dans `data/`) sont dans le répertoire de votre projet PlatformIO.
2.  **Configuration PlatformIO:** Vérifiez le fichier `platformio.ini` pour vous assurer que l'environnement cible (ex: `esp32s3box`) est correct.
3.  **Construction du Firmware:**
    ```bash
    pio run -e <votre_environnement>
    ```
4.  **Téléchargement du Système de Fichiers (Web UI):**
    ```bash
    pio run -e <votre_environnement> -t uploadfs
    ```
5.  **Téléchargement du Firmware:**
    ```bash
    pio run -e <votre_environnement> -t upload
    ```

### 3.3. Utilisation de la Plateforme

1.  Après le flashage, l'ESP32 démarrera un Point d'Accès (AP).
    *   **SSID par défaut:** `ESP32_Security_Platform_v4`
    *   **Mot de passe par défaut:** `password123`
2.  Connectez votre ordinateur ou téléphone à ce réseau WiFi.
3.  Ouvrez votre navigateur et naviguez vers l'adresse: `http://192.168.4.1`
4.  L'interface web avancée se chargera. Utilisez le menu latéral pour accéder aux différents modules d'attaque (Deauth, Fuzzing, WPA3 Capture, Scanner).
5.  **Pour le Red Team :** Utilisez les modules d'attaque pour simuler des scénarios de compromission.
6.  **Pour le Blue Team :** Utilisez le **Router Vulnerability Scanner** pour identifier les faiblesses de votre propre réseau de défense (et les corriger), puis utilisez les logs pour détecter les tentatives d'attaque.

## 4. Intégration du Code C++ (Aperçu)

Les nouveaux modules C++ ont été ajoutés dans les répertoires `include/` et `src/`.

### 4.1. `DeauthAttackAdvanced`

Ce module utilise la fonction de transmission de trames brutes 802.11 (simulée par `esp_wifi_80211_tx` dans l'implémentation) pour envoyer des paquets de déauthentification.

### 4.2. `FrameFuzzer`

Le fuzzing est effectué en construisant des trames 802.11 standard (comme les Beacons) et en appliquant des mutations aléatoires aux octets de la trame avant l'envoi.

### 4.3. `WPA3HandshakeCapture`

Ce module met l'ESP32 en mode promiscuité (`esp_wifi_set_promiscuous(true)`) et utilise un *sniffer callback* pour intercepter et analyser les trames 802.11. Il est configuré pour détecter spécifiquement les échanges SAE (WPA3) et les exporter aux formats standard (`HCCAPX` et `PCAP`) pour un cracking hors ligne.

## 5. Conclusion pour le Projet Universitaire

Cette plateforme v4.0 fournit un outil puissant et pédagogique pour votre projet Red Team vs Blue Team.

*   **Red Team:** Dispose d'un arsenal d'attaques réalistes (DoS, Fuzzing, Capture de Handshake) pour pénétrer ou déstabiliser le réseau cible.
*   **Blue Team:** Dispose d'un outil de diagnostic (Vulnerability Scanner) et d'un système de surveillance (Logs) pour détecter, analyser et corriger les vulnérabilités en temps réel.

N'oubliez pas d'utiliser cet outil de manière éthique et uniquement sur des réseaux pour lesquels vous avez une autorisation explicite, comme stipulé par votre université.

