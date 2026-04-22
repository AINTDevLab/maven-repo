# AINTDevLab Maven Repository

Repositorio Maven alojado en GitHub. Este repositorio **no contiene código fuente**: su única finalidad es servir artefactos (`.jar`, `.pom`, `.xml` de metadatos, etc.) para poder declararlos como dependencias en otros proyectos Maven.

La URL base del repositorio (modo *raw*) es:

```
https://raw.githubusercontent.com/AINTDevLab/maven-repo/main/
```

---

## Usar artefactos como dependencia

Para consumir artefactos publicados aquí, añade el repositorio a tu `pom.xml` y declara la dependencia como cualquier otra.

### 1. Declarar el repositorio

```xml
<repositories>
    <repository>
        <id>aintdevlab-maven-repo</id>
        <name>AINTDevLab Maven Repository</name>
        <url>https://raw.githubusercontent.com/AINTDevLab/maven-repo/main/</url>
    </repository>
</repositories>
```

Si el artefacto es un plugin Maven, declara además:

```xml
<pluginRepositories>
    <pluginRepository>
        <id>aintdevlab-maven-repo</id>
        <url>https://raw.githubusercontent.com/AINTDevLab/maven-repo/main/</url>
    </pluginRepository>
</pluginRepositories>
```

### 2. Declarar la dependencia

```xml
<dependency>
    <groupId>com.ejemplo</groupId>
    <artifactId>mi-artefacto</artifactId>
    <version>1.0.0</version>
</dependency>
```

> El repositorio es público, no se requiere autenticación para consumir artefactos.

---

## Publicar artefactos

La forma recomendada es desplegar el artefacto a un **directorio local** que sea un clon de este repositorio, y luego hacer `commit` y `push`.

### Opción A — Desplegar con `mvn deploy` a un directorio local

1. Clona este repositorio junto al proyecto que quieres publicar:

    ```bash
    git clone https://github.com/AINTDevLab/maven-repo.git
    ```

2. En el `pom.xml` del proyecto a publicar, configura `distributionManagement` apuntando al clon local mediante el protocolo `file://`:

    ```xml
    <distributionManagement>
        <repository>
            <id>aintdevlab-maven-repo</id>
            <name>AINTDevLab Maven Repository</name>
            <url>file://${project.basedir}/../maven-repo</url>
        </repository>
    </distributionManagement>
    ```

    Ajusta la ruta relativa según dónde tengas clonado este repositorio.

3. Despliega el artefacto:

    ```bash
    mvn clean deploy
    ```

    Maven generará la estructura de directorios (`groupId/artifactId/version/...`) dentro del clon local.

4. Entra en el clon local de `maven-repo`, confirma los cambios y súbelos:

    ```bash
    cd ../maven-repo
    git add .
    git commit -m "Publica com.ejemplo:mi-artefacto:1.0.0"
    git push origin main
    ```

Una vez subidos los cambios, el artefacto queda inmediatamente disponible a través de la URL *raw* de GitHub.

### Opción B — Instalar manualmente un JAR de terceros

Si necesitas publicar un `.jar` que no se construye con Maven (por ejemplo, una librería de un tercero), usa `install:install-file` apuntando a una copia local de este repositorio:

```bash
mvn install:install-file \
    -Dfile=ruta/a/libreria.jar \
    -DgroupId=com.ejemplo \
    -DartifactId=mi-artefacto \
    -Dversion=1.0.0 \
    -Dpackaging=jar \
    -DlocalRepositoryPath=/ruta/al/clon/de/maven-repo
```

Después, `commit` y `push` como en la Opción A.

---

## Estructura del repositorio

Los artefactos se almacenan siguiendo el layout estándar de Maven 2:

```
<groupId convertido a path>/<artifactId>/<version>/<artifactId>-<version>.<ext>
```

Ejemplo:

```
com/ejemplo/mi-artefacto/1.0.0/mi-artefacto-1.0.0.jar
com/ejemplo/mi-artefacto/1.0.0/mi-artefacto-1.0.0.pom
com/ejemplo/mi-artefacto/maven-metadata.xml
```

---

## Convenciones

- Usa **versiones inmutables**: una vez publicada una versión, no la sobrescribas. Para cambios, incrementa la versión.
- Evita publicar `SNAPSHOT` salvo para pruebas internas; al ser un repo *raw* no hay resolución dinámica de snapshots.
- Revisa siempre el `diff` antes de hacer `push` para no subir archivos ajenos al layout Maven.
