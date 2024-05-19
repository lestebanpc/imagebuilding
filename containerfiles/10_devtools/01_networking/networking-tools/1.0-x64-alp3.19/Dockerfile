#Ejemplo del comando de creación/compilación de la imagen:
#  > podman build --build-arg UID=1000 --build-arg GID=1000 -t lucianoepc/networking-tools:1.0-x64-alp3.19 ./workdir
#

#-------------------------------------------------------------------------------------
# Layer intermedia
#-------------------------------------------------------------------------------------
FROM alpine:3.19.1 AS builder

#1. Instalar programas basicos y archivos del profile del usuario
RUN apk --no-cache add bash git curl \
    && git clone https://github.com/lestebanpc/dotfiles.git ~/.files

#2. Descargar el VIM de plugins y comandos indicando en parametro.
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
#  > Opción de menu a usar: (131072) Descargar plugins de Editor de VIM
RUN bash ~/.files/setup/linux/00_setup_summary.bash 2 131072 'EMPTY' 'EMPTY' 'EMPTY' 'EMPTY' 'EMPTY' -1 1 1 ""


#-------------------------------------------------------------------------------------
# Layer final
#-------------------------------------------------------------------------------------
FROM alpine:3.19.1 as final

#1. Parametros de entrada
ARG UID
ARG GID

#2. Constantes
ARG USER_NAME=appuser
ARG HOME_PATH=/home/appuser

#3. Crear el usuario/grupo para desarrollo e instalar git/sudo y remplazando los comandos de networking tools predeterminados de BusyBox.
RUN apk --no-cache add bash sudo curl openssh-client wget \
    bind-tools dhclient net-tools net-tools bridge-utils ifupdown-ng iputils iproute2-minimal netcat-openbsd traceroute \
    iptables ip6tables iproute2 tcpdump dhcping \
    && addgroup -g $GID ${USER_NAME} \
    && adduser -D -u $UID -G ${USER_NAME} -h ${HOME_PATH} -s /bin/bash ${USER_NAME} \
    && mkdir -p ${HOME_PATH} \
    && chown $UID:$GID ${HOME_PATH} \
    && mkdir -pm 755 /opt/tools/ \
    && chown $UID:$GID /opt/tools/ \ 
    && mkdir -m 755 ${HOME_PATH}/.files/ \
    && chown $UID:$GID ${HOME_PATH}/.files/ \
    && mkdir -m 755 ${HOME_PATH}/.vim/ \
    && chown $UID:$GID ${HOME_PATH}/.vim/ \
    && echo "${USER_NAME} ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

#4. Copiar archivos de usuario y los plugins descargdos.
COPY --chown=${UID}:${GID} --from=builder /root/.files/ ${HOME_PATH}/.files/
COPY --chown=${UID}:${GID} --from=builder /root/.vim/ ${HOME_PATH}/.vim/

#5. Instalar programas basicos (VIM, OpenSSL, FzF) y comandos basicos. Luego, para el usuario, descargar y configurar a VIM como Editor
#   Todas las opciones, debido a que tiene soporte a SUDO sin password
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
#  > Opción de menu a usar: Instalar VIM (32768) + Instalar paquetes basicos (1024) + Crear archivos de configuración y indexar plugins (64) + User profile (1)
USER ${UID}:${GID}
RUN bash ${HOME_PATH}/.files/setup/linux/00_setup_summary.bash 2 33857 'jq,yq,fd,bat,ripgrep,oh-my-posh' 'openssl,fzf' 'EMPTY' 'EMPTY' 'EMPTY' -1 0 1 "$UID:$GID"

#6. Cambiar el shell predeterminado 'ash' a 'bash'
ENTRYPOINT [ "/bin/bash" ]



