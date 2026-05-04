# setup

Playbook de [Ansible](https://www.ansible.com/) para instalar de forma **opcional** herramientas de desarrollo y utilidades en **localhost** (Ubuntu/Debian). Cada bloque de tareas se activa con **tags**; no hace falta instalar todo de una.

## Requisitos

- Ubuntu o Debian con `sudo`
- Python 3 en la máquina donde corrés el playbook
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) instalado

## Uso

Desde el directorio del repositorio:

```bash
ansible-playbook setup.yml --list-tags
```

Para ejecutar solo lo que necesitás, pasá los tags y, si las tareas usan `sudo`, la contraseña con `-K`:

```bash
ansible-playbook setup.yml -K --tags "git,frida"
```

- **`--tags`**: solo se aplican las tareas con esos tags.
- **`-K` / `--ask-become-pass`**: pide la contraseña de sudo (apt, instalación de `.deb`, etc.).

Ejemplos:

| Objetivo | Comando |
| -------- | ------- |
| Solo Git | `ansible-playbook setup.yml -K --tags git` |
| Solo Proton VPN (app GNOME) | `ansible-playbook setup.yml -K --tags proton-vpn` |
| Git y Proton juntos | `ansible-playbook setup.yml -K --tags git-proton` |
| Varios componentes | `ansible-playbook setup.yml -K --tags "aws-cli,nvm-node,cursor"` |
| Excluir algo | `ansible-playbook setup.yml -K --tags nvm-node --skip-tags chrome` |

## Tags principales

| Tag | Contenido |
| --- | --- |
| `aws-cli`, `aws` | AWS CLI |
| `android-studio`, `android` | Android Studio |
| `adb`, `android-debug-bridge` | ADB |
| `httptoolkit`, `http-toolkit` | HTTPToolkit |
| `nvm-node`, `nvm`, `node` | NVM y Node |
| `frida`, `frida-tools` | Frida (pip usuario + PATH) |
| `git` | Git (APT) |
| `proton-vpn`, `proton`, `vpn` | Proton VPN (repositorio oficial + app GNOME en **amd64**) |
| `git-proton` | Git **y** Proton VPN (equivale a usar ambos bloques) |
| `chrome`, `google-chrome` | Google Chrome |
| `cursor` | Cursor |
| `postman` | Postman |

Listado completo en cualquier momento:

```bash
ansible-playbook setup.yml --list-tags
```

## Variables (`group_vars/all.yml`)

Versiones y URLs por defecto (podés sobrescribir con `-e var=valor` al ejecutar el playbook):

- `frida_version`
- `httptoolkit_deb_url`, `cursor_deb_url`, `chrome_deb_url`
- `nvm_node_version`
- `protonvpn_release_deb_url` — paquete `protonvpn-stable-release` que añade el repositorio APT de Proton; actualizalo si Proton publica una versión nueva del `.deb`.

## Estructura

- `setup.yml` — playbook principal; importa los ficheros bajo `tasks/`.
- `tasks/*.yml` — un bloque lógico por herramienta (AWS CLI, Git, Frida, etc.).
- `group_vars/all.yml` — variables por defecto.

## Notas

- **Proton VPN**: la app gráfica oficial (`proton-vpn-gnome-desktop`) sigue la documentación de Proton para Ubuntu y está pensada para **amd64**. En otras arquitecturas el playbook no instala la GUI; podés usar el [CLI de Proton VPN](https://protonvpn.com/support/linux-cli) manualmente.
- Las tareas usan `ignore_errors: true` en los bloques para que un fallo en un componente no detenga el resto del playbook si ejecutás varios tags a la vez; revisá la salida de Ansible si algo no se instaló.
