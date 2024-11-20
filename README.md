# Jenkins és Docker Registry Telepítése Ansible segítségével

Ez a projekt egy Ansible playbookot tartalmaz, amely telepíti a Jenkins szervert, beállít egy privát Docker registry-t és annak webes UI-ját, valamint létrehozza a szükséges GitHub repository-t és egy Jenkins pipeline-t a webalkalmazás buildeléséhez, tárolásához és deploy-jához.

## Előfeltételek

- **Ansible** telepítése a helyi gépen
- **Docker** és **Docker Compose** telepítése a célgépen
- **GitHub Personal Access Token** (PAT) a privát repository létrehozásához
- **GitHub fiók** a repository kezeléséhez
- **Célgép**: Ubuntu 22.04 (vagy más Linux alapú OS)
- A célgépen szükséges, hogy legyen Docker és Docker Compose telepítve

## Telepítés

1. **Clone-oljuk a repository-t** a következő paranccsal:
   ```bash
   git clone <repo_url>
   cd <repo_name>
   ```

2. **Szükséges fájlok módosítása**:
    - A `vars` szekcióban állítsd be a következő változókat:
        - `github_token`: A GitHub PAT tokened
        - `github_username`: A GitHub felhasználóneved
        - `repo_name`: A létrehozni kívánt repository neve
        - `registry_name`: A Docker registry neve
        - `registry_ui_name`: A Docker registry UI neve
        - `jenkins_port`: A Jenkins elérhetősége (alapértelmezetten `8081`)
        - `worker_name`: A Jenkins worker neve

3. **Playbook futtatása**:
   Az alábbi parancs segítségével futtathatjuk a playbookot a célgépen:
   ```bash
   ansible-playbook -i <inventory_file> jenkins_docker_registry.yml
   ```

## Lépések

A playbook a következő lépéseket hajtja végre:

1. **Docker telepítése**:
   A Docker és Docker Compose telepítése és frissítése a célgépen.

2. **Docker Registry és UI elindítása**:
   Két Docker konténer indítása:
    - Egy privát Docker registry (`registry:2`)
    - Docker registry UI (`jc21/registry-ui`)

3. **Jenkins telepítése**:
    - Jenkins master konténer indítása
    - Jenkins worker konténer indítása

4. **Privát GitHub repository létrehozása**:
    - A playbook egy privát GitHub repository-t hoz létre, amely tartalmazni fogja a `hello-world` alkalmazást.

5. **Hello World alkalmazás előkészítése**:
    - Létrejön egy Python alapú webalkalmazás Flask keretrendszerrel, amely egy "Hello, World!" üzenetet szolgáltat.
    - A projekt tartalmaz egy `Dockerfile`-t is, amivel létrehozható a szükséges Docker image.

6. **GitHub repository-ba feltöltés**:
    - A projekt kódja feltöltésre kerül a létrehozott GitHub repository-ba.

7. **Jenkins pipeline konfigurálása**:
    - A Jenkinsfile a következő lépéseket tartalmazza:
        - **Clone**: A kód klónozása a GitHub repository-ból
        - **Build**: Docker image építése a `Dockerfile` alapján
        - **Push**: Az image feltöltése a privát Docker registry-be
        - **Deploy**: Az alkalmazás indítása a registry-ből egy új Docker konténerben

8. **Jenkins job hozzáadása**:
    - A playbook a létrehozott Jenkinsfile alapján beállít egy Jenkins pipeline jobot, amely automatikusan elindítja a build és deploy folyamatokat.

## Jenkins Pipeline

A Jenkins pipeline automatikusan végrehajtja a következő lépéseket:

1. **Kód klónozása**: A GitHub repository-ból történik.
2. **Docker build**: A Dockerfile alapján építi a szükséges image-et.
3. **Image feltöltése**: Az elkészült image a Docker registry-be kerül.
4. **Deploy**: Az alkalmazás elindítása a registry-ből egy új Docker konténerben.

## Hibakeresés

- Ellenőrizd, hogy a GitHub token helyes-e és van hozzáférésed a repository-hoz.
- Ha bármilyen portkonfliktust tapasztalsz, ellenőrizd, hogy a megfelelő portok nincsenek-e foglalva más szolgáltatások által.
- A Jenkins UI elérhetősége: `http://<server_ip>:8081`

## Extra Funkciók

A pipeline konfigurálható, hogy több lépést is tartalmazzon, például:
- Tesztelési lépések
- Részletesebb build logok
- Speciális deploy lépések (pl. Kubernetes, AWS)

## License

Ez a projekt MIT licenc alatt áll.
