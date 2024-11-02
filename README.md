#!/bin/bash

VERDE='\033[0;32m'
ROJO='\033[0;31m'
NC='\033[0m'

crear_usuario() {
    clear
    echo "=== Crear Usuario Nuevo ==="
    echo
    read -p "Ingrese nombre del nuevo usuario: " nombre_usuario
    
    if id "$nombre_usuario" >/dev/null 2>&1; then
        echo -e "${ROJO}Error: El usuario ya existe${NC}"
    else
        sudo useradd -m -s /bin/bash "$nombre_usuario"
        
        echo -e "${VERDE}Establezca una contraseña para $nombre_usuario${NC}"
        sudo passwd "$nombre_usuario"
        
        if [ ! -d "/home/$nombre_usuario" ]; then
            sudo mkdir -p "/home/$nombre_usuario"
            sudo chown "$nombre_usuario:$nombre_usuario" "/home/$nombre_usuario"
        fi
        
        echo -e "${VERDE}Usuario creado exitosamente${NC}"
    fi
}

verificar_actualizaciones() {
    clear
    echo "=== Verificar Actualizaciones ==="
    echo
    sudo apt update

    echo
    apt list --upgradable
    
    echo
    read -p "¿Desea instalar las actualizaciones? (s/n): " respuesta
    if [ "$respuesta" = "s" ]; then
        sudo apt upgrade -y
        echo -e "${VERDE}Actualizaciones instaladas${NC}"
    fi
}

ver_estado_sistema() {
    clear
    echo "=== Informe del Sistema ==="
    echo
    log_file="informe_sistema_$(date +%Y%m%d_%H%M).txt"
    
    echo "Generando informe en $log_file ..."
    
    {
        echo "=== INFORME DEL SISTEMA ==="
        echo "Fecha: $(date)"
        echo
        echo "=== USO DE CPU ==="
        top -bn1 | head -n 5
        echo
        echo "=== MEMORIA RAM ==="
        free -h
        echo
        echo "=== ESPACIO EN DISCO ==="
        df -h
        
    } > "$log_file"
    
    echo -e "${VERDE}Informe guardado en: $log_file${NC}"
    echo
    cat "$log_file"
}

while true; do
    clear
    echo "=============================="
    echo "     MENÚ DE ADMINISTRACIÓN   "
    echo "=============================="
    echo "1. Crear usuario nuevo"
    echo "2. Verificar actualizaciones"
    echo "3. Generar informe del sistema"
    echo "4. Salir"
    echo
    read -p "Elija una opción (1-4): " opcion
    echo

    case $opcion in
        1)
            crear_usuario
            ;;
        2)
            verificar_actualizaciones
            ;;
        3)
            ver_estado_sistema
            ;;
        4)
            echo "¡Hasta luego!"
            exit 0
            ;;
        *)
            echo -e "${ROJO}Opción inválida${NC}"
            ;;
    esac

    echo
    read -p "Presione Enter para continuar..."
done
