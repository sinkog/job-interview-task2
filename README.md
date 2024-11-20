# Ansible UDEMX Szerepkör Telepítés

Ez a projekt egy Ansible playbook, amely automatizálja az alábbi szolgáltatások telepítését és konfigurálását:

- **NGINX**:
  - Webszerver telepítése a 80-as portra.
  - EXTRA: HTTPS beállítása self-signed tanúsítvánnyal.
  - A főoldalon megjelenik: `Hello UDEMX!`.

- **MariaDB**:
  - Adatbázis-szerver telepítése az alapértelmezett portra.
  - EXTRA: Létrehoz egy felhasználót (`udemx`) és egy adatbázist (`udemx-db`), valamint beállítja a megfelelő jogosultságokat.

- **Docker**:
  - Telepíti a Dockert és futtatja a `hello-world` konténert.
  - EXTRA1: Opcionálisan az NGINX és MariaDB konténerekben futtatása.
  - EXTRA2: A konténerek automatikusan elindulnak a virtuális géppel.

- **Git**:
  - Telepíti a Gitet és beállítja az alapértelmezett felhasználót (`udemx@udemx.eu`).
  - EXTRA1: SSH kulcs alapú Git elérés beállítása.

- **Iptables beállítása**:
  - Csak az alkalmazásokhoz szükséges portok legyenek nyitva a virtuális gépen.

- **Shell script feladatok**:
  - **MySQL dump készítése** a napi mentéshez.
  - **Legutóbbi fájlok listázása** a `/var/log` mappából.
  - **Load average kiíratása** a rendszer állapotáról.
  - **NGINX konfiguráció** módosítása, hogy a `<title>` sztringben az `Welcome to nginx!` helyett `Title: ` szerepeljen.

- **Docker Compose** segítségével Laravel demo alkalmazás futtatása külön adatbázissal és webszerverrel.

## Előfeltételek

- Linux-alapú operációs rendszer (tesztelve Ubuntu/Debian rendszeren).
- Telepített Python 3.
- Sudo jogosultság a playbookot futtató felhasználónak.

### Python 3 és szükséges komponensek telepítése

A rendszer előkészítéséhez futtasd a következő parancsokat:

```bash
sudo apt update
sudo apt install -y python3 python3-pip
pip3 install ansible docker pymysql
```

### Opcionális: Python virtuális környezet használata

```bash
python3 -m venv ansible-env
source ansible-env/bin/activate
pip install ansible docker pymysql
```

## Projektstruktúra

```
ansible-project/
├── inventory.ini          # Ansible inventory fájl
├── playbook.yml           # Fő playbook
└── roles/
|   └── udemx_role/
|       ├── tasks/         # Feladatdefiníciók
|       │   ├── main.yml   # Fő feladatok importálása
|       │   ├── nginx.yml  # NGINX telepítés
|       │   ├── mariadb.yml # MariaDB telepítés
|       │   ├── docker.yml # Docker telepítés
|       │   └── git.yml    # Git telepítés
│       │   ├── iptables.yml# Iptables beállítása
│       │   └── scripts.yml # Shell scriptek és cron beállítások
|       ├── templates/     # Konfigurációs sablonok
|       │   ├── index.html.j2    # NGINX HTML sablon
|       │   └── ssl_cert.sh.j2   # Self-signed tanúsítvány script
|       └── handlers/      # Szolgáltatások újraindítása
└── README.md               # A projekt dokumentációja
```

## Használat

1. **Projekt klónozása vagy létrehozása**  
   Klónozd ezt a repót, vagy hozd létre a szükséges mappa struktúrát.

2. **Inventory fájl szerkesztése**  
   Frissítsd az `inventory.ini` fájlt a célgépek hozzáadásához. Helyi teszteléshez:

   ```ini
   [all]
   localhost ansible_connection=local
   ```

3. **Playbook futtatása**

   Futtasd a playbookot az Ansible-lel:

   ```bash
   ansible-playbook -i inventory.ini playbook.yml
   ```

   Add meg a sudo jelszót, ha kéri.

## Főbb funkciók

- **NGINX**:
    - Automatikusan beállítja a webszervert HTTP és HTTPS támogatással.
    - Egyszerű kezdőoldalt biztosít.

- **MariaDB**:
    - Felhasználó- és adatbázis-létrehozás jogosultságkezeléssel.

- **Docker**:
    - Telepíti a Dockert és futtat egy alapértelmezett konténert.

- **Git**:
    - Globális Git-beállításokat végez és SSH-kulcs alapú hitelesítést konfigurál.

- **Vim kilépés**:
    - :q :quit egyszerű kilépés (ha változtattál valamit a file-ben akkor nem engedi kilépni)
    - :q! :quit! változtatások elsobásával való kilépés
    - :wq :x ment és kilép

- **Iptables beállítása**:
    - 80 (HTTP), 443 (HTTPS), 3306 (MariaDB) és 22 (SSH) portok nyitása.
    - Az összes egyéb port blokkolása.

- **Shell scriptek és cron beállítások**
    - **MySQL dump készítése**, amely minden nap hajnali 2 órakor lefut.
    - A **3 legutóbb módosított fájl listázása** a `/var/log` mappából.
    - A **5 napon belül módosított fájlok listázása** rekurzívan a `/var/log/*` mappákból.
    - A **load average** kiíratása a `/proc/loadavg` fájlból.
    - Az **NGINX konfigurációs fájlban** a `<title>` tag módosítása a kívánt értékre.

- **Docker Compose Laravel Demo Alkalmazás**
    - Laravel alkalmazás (web) konténer.
    - MariaDB adatbázis konténer.

Ez biztosítja az alkalmazás elkülönítését és skálázhatóságát.
## Megjegyzések

- A playbook sudo jogosultságot igényel a futtató felhasználótól. Győződj meg arról, hogy az `ansible_become_pass` meg van adva a futtatás során.
- Éles környezetben a self-signed tanúsítvány helyett érvényes SSL tanúsítványt használj.

