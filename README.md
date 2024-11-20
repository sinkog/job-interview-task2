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
    └── udemx_role/
        ├── tasks/         # Feladatdefiníciók
        │   ├── main.yml   # Fő feladatok importálása
        │   ├── nginx.yml  # NGINX telepítés
        │   ├── mariadb.yml # MariaDB telepítés
        │   ├── docker.yml # Docker telepítés
        │   └── git.yml    # Git telepítés
        ├── templates/     # Konfigurációs sablonok
        │   ├── index.html.j2    # NGINX HTML sablon
        │   └── ssl_cert.sh.j2   # Self-signed tanúsítvány script
        └── handlers/      # Szolgáltatások újraindítása
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

## Megjegyzések

- A playbook sudo jogosultságot igényel a futtató felhasználótól. Győződj meg arról, hogy az `ansible_become_pass` meg van adva a futtatás során.
- Éles környezetben a self-signed tanúsítvány helyett érvényes SSL tanúsítványt használj.

## Licenc

Ez a projekt nyílt forráskódú, és az MIT Licenc alatt érhető el.
