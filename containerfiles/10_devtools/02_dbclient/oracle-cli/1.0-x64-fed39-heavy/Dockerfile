#Ejemplo del comando de creación/compilación de la imagen:
#  > podman build --build-arg UID=1000 --build-arg GID=1000 -t quyllur/oracle-cli-fed39:1.0-x64 ./workdir
#

#-------------------------------------------------------------------------------------
# Layer final
#-------------------------------------------------------------------------------------
FROM registry.access.redhat.com/ubi8/ubi:8.9

#1. Parametros de entrada
ARG UID
ARG GID

#2. Constantes
ARG USER_NAME=appuser
ARG HOME_PATH=/home/appuser
ARG PCKG_BASE_URL='https://download.oracle.com/otn_software/linux/instantclient/2113000/instantclient-basic-linux.x64-21.13.0.0.0dbru.zip'
ARG PCKG_SQLPLUS_URL='https://download.oracle.com/otn_software/linux/instantclient/2113000/instantclient-sqlplus-linux.x64-21.13.0.0.0dbru.zip'
ARG PCKG_TOOLS_URL='https://download.oracle.com/otn_software/linux/instantclient/2113000/instantclient-tools-linux.x64-21.13.0.0.0dbru.zip'
ARG PCKG_SDK_INSTALL=0
ARG PCKG_SDK_URL='https://download.oracle.com/otn_software/linux/instantclient/2113000/instantclient-sdk-linux.x64-21.13.0.0.0dbru.zip'

#2. Instalar programas basicos y crear el usuario/grupo para la aplicación
RUN dnf -y install git sudo curl unzip libaio \
    && dnf -y clean all \
    && groupadd -g $GID ${USER_NAME} \
    && useradd -u $UID -g ${USER_NAME} -d ${HOME_PATH} -s /bin/bash ${USER_NAME} \
    && mkdir -p ${HOME_PATH} \
    && chown $UID:$GID ${HOME_PATH} 
    #&& echo "${USER_NAME} ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers \

#3. Instalar el 'Oracle Instant Client' v21.13
RUN mkdir -pm 755 /opt/tools/oracle/db/client \
    && mkdir -m 755 /opt/tools/oracle/db/client/libext/ \
    && mkdir -m 755 /opt/tools/oracle/db/client/bin/ \
    && curl -fLo package-basic.zip ${PCKG_BASE_URL} \
    && unzip package-basic.zip -d /opt/tools/oracle/db/client \
    && curl -fLo package-sqlplus.zip ${PCKG_SQLPLUS_URL} \
    && unzip package-sqlplus.zip -d /opt/tools/oracle/db/client \
    && curl -fLo package-tools.zip ${PCKG_TOOLS_URL} \
    && unzip package-tools.zip -d /opt/tools/oracle/db/client \
    && if [ ${PCKG_SDK_INSTALL} -eq 0 ]; then \
        curl -fLo package-sdk.zip ${PCKG_SDK_URL}; \
        unzip package-sdk.zip -d /opt/tools/oracle/db/client; \
        rm package-sdk.zip; \
    fi \
    && rm package-basic.zip package-sqlplus.zip package-tools.zip \
    && mv /opt/tools/oracle/db/client/instantclient_21_13 /opt/tools/oracle/db/client/lib/ \
    && if [ ${PCKG_SDK_INSTALL} -eq 0 ]; then \
        mv /opt/tools/oracle/db/client/lib/sdk/  /opt/tools/oracle/db/client/; \
    fi \
    && mv /opt/tools/oracle/db/client/lib/*_LICENSE   /opt/tools/oracle/db/client/ \
    && mv /opt/tools/oracle/db/client/lib/*_README    /opt/tools/oracle/db/client/ \
    && mv /opt/tools/oracle/db/client/lib/network/    /opt/tools/oracle/db/client/ \ 
    && chown -R $UID:$GID /opt/tools/oracle/db/client/network/ \ 
    && mv /opt/tools/oracle/db/client/lib/*.jar       /opt/tools/oracle/db/client/libext/ \
    && mv /opt/tools/oracle/db/client/lib/adrci       /opt/tools/oracle/db/client/bin/ \
    && mv /opt/tools/oracle/db/client/lib/genezi      /opt/tools/oracle/db/client/bin/ \
    && mv /opt/tools/oracle/db/client/lib/glogin.sql  /opt/tools/oracle/db/client/bin/ \
    && mv /opt/tools/oracle/db/client/lib/sqlplus     /opt/tools/oracle/db/client/bin/ \
    && mv /opt/tools/oracle/db/client/lib/uidrvci     /opt/tools/oracle/db/client/bin/ \
    && mv /opt/tools/oracle/db/client/lib/exp         /opt/tools/oracle/db/client/bin/ \
    && mv /opt/tools/oracle/db/client/lib/expdp       /opt/tools/oracle/db/client/bin/ \
    && mv /opt/tools/oracle/db/client/lib/imp         /opt/tools/oracle/db/client/bin/ \
    && mv /opt/tools/oracle/db/client/lib/impdp       /opt/tools/oracle/db/client/bin/ \
    && mv /opt/tools/oracle/db/client/lib/sqlldr      /opt/tools/oracle/db/client/bin/ \
    && mv /opt/tools/oracle/db/client/lib/wrc         /opt/tools/oracle/db/client/bin/ \
    && echo /opt/tools/oracle/db/client/lib > /etc/ld.so.conf.d/oracle-instantclient.conf \
    && ldconfig

#3. Descargar archivos del usuario
USER ${UID}:${GID}
RUN git clone https://github.com/lestebanpc/dotfiles.git ${HOME_PATH}/.files

#4. Instalar paquetes basicos adicionales (openssl y VIM) y comandos basicos
#   > Parametros de '00_setup_summary.bash':
#     01> Tipo de invocación (sin menu): 1/2 (sin usar un menu interactivo/no-interactivo)
#     02> Opciones de menu a ejecutar: entero positivo.
#     03> ID de los repositorios de comandos a configurar, separados por coma. Si no desea configurarse ninguno envie "EMPTY".
#     04> ID de los paquetes del repositorio del SO, separados por coma, a instalar si elige la opcion de menu 1024. Si desea usar el los los paquetes por defecto envie "EMPTY".
#         Los paquetes por defecto que son: Curl, UnZip, OpenSSL y Tmux.
#     05> Ruta donde se descargaran los programas (de repositorios como github). Si se envia vacio o EMPTY se usara el directorio predeterminado "/opt/tools" o "~/tools".
#     06> Ruta base donde se almacena los comandos ("CMD_PATH_BASE/bin"), archivos man1 ("CMD_PATH_BASE/man/man1") y fonts ("CMD_PATH_BASE/share/fonts").
#         Si se envia vacio o EMPTY se usara el directorio predeterminado.
#            > Comandos      : "/usr/local/bin"      (para todos los usuarios) y "~/.local/bin"         (solo para el usuario actual)
#            > Archivos man1 : "/usr/local/man/man1" (para todos los usuarios) y "~/.local/man/man1"    (solo para el usuario actual)
#            > Archivo fuente: "/usr/share/fonts"    (para todos los usuarios) y "~/.local/share/fonts" (solo para el usuario actual)
#     07> Ruta de archivos temporales. Si se envia vacio o EMPTY se usara el directorio predeterminado.
#     08> El estado de la credencial almacenada para el sudo.
#     09> Flag '0' para limpiar el cache de paquetes del sistema operativo. Caso contrario, use 1.
#     10> Actualizar los paquetes del SO. Por defecto es 1 (false), si desea actualizar use 0.
#     11> El GID y UID del usuario que ejecuta el script, siempre que no se el owner de repositorio, en formato "UID:GID"
#  > Opción de menu a usar: Paquetes basicos 'openssl' (1024) + VIM (32768) 
USER 0:0
RUN bash ${HOME_PATH}/.files/setup/linux/00_setup_summary.bash 2 33792 'jq,yq,fd,fzf,bat,ripgrep,oh-my-posh' 'openssl' 'EMPTY' 'EMPTY' 'EMPTY' -1 0 1 "$UID:$GID"

#5. Instalar el profile como Editor
#   > Parametros de '00_setup_summary.bash':
#     01> Tipo de invocación (sin menu): 1/2 (sin usar un menu interactivo/no-interactivo)
#     02> Opciones de menu a ejecutar: entero positivo.
#     03> ID de los repositorios de comandos a configurar, separados por coma. Si no desea configurarse ninguno envie "EMPTY".
#     04> ID de los paquetes del repositorio del SO, separados por coma, a instalar si elige la opcion de menu 1024. Si desea usar el los los paquetes por defecto envie "EMPTY".
#         Los paquetes por defecto que son: Curl, UnZip, OpenSSL y Tmux.
#     05> Ruta donde se descargaran los programas (de repositorios como github). Si se envia vacio o EMPTY se usara el directorio predeterminado "/opt/tools" o "~/tools".
#     06> Ruta base donde se almacena los comandos ("CMD_PATH_BASE/bin"), archivos man1 ("CMD_PATH_BASE/man/man1") y fonts ("CMD_PATH_BASE/share/fonts").
#         Si se envia vacio o EMPTY se usara el directorio predeterminado.
#            > Comandos      : "/usr/local/bin"      (para todos los usuarios) y "~/.local/bin"         (solo para el usuario actual)
#            > Archivos man1 : "/usr/local/man/man1" (para todos los usuarios) y "~/.local/man/man1"    (solo para el usuario actual)
#            > Archivo fuente: "/usr/share/fonts"    (para todos los usuarios) y "~/.local/share/fonts" (solo para el usuario actual)
#     07> Ruta de archivos temporales. Si se envia vacio o EMPTY se usara el directorio predeterminado.
#     08> El estado de la credencial almacenada para el sudo.
#     09> Flag '0' para limpiar el cache de paquetes del sistema operativo. Caso contrario, use 1.
#     10> Actualizar los paquetes del SO. Por defecto es 1 (false), si desea actualizar use 0.
#     11> El GID y UID del usuario que ejecuta el script, siempre que no se el owner de repositorio, en formato "UID:GID"
#  > Opción de menu a usar: Configurar el profile del usuario (1) + VIM como Editor 'Crear archivos, Descargar plugins e indexarlos' (4)
USER ${UID}:${GID}
RUN bash ${HOME_PATH}/.files/setup/linux/00_setup_summary.bash 2 5 'EMPTY' 'EMPTY' 'EMPTY' 'EMPTY' 'EMPTY' -1 1 1 "$UID:$GID"

#6. Establecer el PATH los comandos del Oracle Client
ENV PATH="$PATH:/opt/tools/oracle/db/client/bin"


