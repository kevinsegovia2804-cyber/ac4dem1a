# Creación de la Máquina Virtual de Azure

Hola, a continuación vamos a mostrar el paso a paso de como creamos la maquina virtual en azure for student e instalamos coolify para lograr el despliegue de la aplicación pokedex 



## 1. Preparación y Acceso al Servicio

- Accede al **Portal de Azure** y utiliza la barra de búsqueda para encontrar **"Máquinas virtuales"**.

-   Haz clic en **"Máquinas virtuales"** en la sección de Servicios y luego selecciona **"+ Crear"** para iniciar el asistente de creación.

## 2. Configuración Básica de la Instancia

Define la identidad y recursos principales de la VM:

-   **Proyecto y Nombre:**

    -   Crea un nuevo **Grupo de recursos**
   
    -   Asigna un **Nombre a la VM** 
    
-   **Región y Redundancia:**
    
    -   Selecciona la **Región** más cercana a tu ubicación.
        
    -   Establece la opción de disponibilidad como **"No se requiere redundancia de infraestructura"**.
        
-   **Imagen y Tamaño:**
    
    -   Selecciona la **Imagen** del sistema operativo (sugerencia: `Ubuntu Server 22.04 LTS` o `24.04 LTS` x64 Gen2).
        
    -   Elige el **Tamaño** de la VM con al menos 2 vCPU y 8 GB RAM (ej. `Standard_B2ms` o `Standard_D2s_v3`).
        
-   **Cuenta Administrador:**
    
    -   Elige **"Password"** como tipo de autenticación.
        
    -   Define un **Usuario administrador** y una **Contraseña** segura.
        
-   **Reglas de Entrada (Puertos):**
    
    -   En la sección de puertos de entrada públicos, selecciona **"Permitir puertos seleccionados"**.
        
    -   Asegúrate de que los puertos **HTTP (80)**, **HTTPS (443)**, y **SSH (22)** estén seleccionados.


##  3. Revisión y Despliegue

-   Haz clic en **"Revisar y crear"** para que Azure valide la configuración.
    
-   Verifica que la **Validación haya pasado**.
    
-   Haz clic en **"Crear"** para iniciar el despliegue de tu Máquina Virtual en Azure.
    
-   Una vez creada la VM, **copia la IP pública** asignada, ya que la necesitarás para acceder a Coolify.

## Una vez que tienes tu Máquina Virtual de Azure lista, el siguiente paso es la instalación de **Coolify**.

Este proceso se realiza accediendo a tu VM a través de SSH y ejecutando el script de instalación proporcionado por Coolify.

## 4. Preparación de la Red (Apertura del Puerto 8000)

Coolify requiere el puerto **8000** abierto para su configuración inicial. Aunque los puertos 80, 443 y 22 se abrieron durante la creación de la VM, debes agregar esta regla de red:

-   **Acceder a la VM en Azure:** En el Portal de Azure, ve a tu **Máquina Virtual** y selecciona la sección **"Redes"**.
    
-   **Ir a NSG:** Haz clic en el **"Grupo de seguridad de red"** (NSG) asociado a tu VM.
    
-   **Crear Regla de Entrada:** En la sección "Reglas de puerto de entrada", haz clic en **"+ Agregar regla de puerto de entrada"**.
    
-   **Configurar el Puerto:** Crea una nueva regla con la siguiente configuración:
    
    -   **Prioridad:** 350
        
    -   **Puertos de destino:** 8000
        
    -   **Protocolo:** TCP
        
    -   **Acción:** Allow
        
    -   **Nombre:** CoolifyWebAccess
        
----------

### 5. Conexión a la VM e Instalación por Terminal

Este paso se realiza ejecutando el script oficial de instalación en tu servidor:

-   **Conexión SSH:** Conéctate a tu Máquina Virtual. El método recomendado por el documento es usar el **Azure Cloud Shell** para una conexión sencilla, donde se ejecuta automáticamente el comando SSH.
    
-   **Aceptar Huella Digital:** Si es la primera vez, acepta la huella digital (**fingerprint**) de la conexión cuando se te solicite.
    
-   **Ejecutar Instalación:** Una vez conectado a la VM, ejecuta el siguiente comando oficial:
    
    Bash
    
    curl -fsSL https://cdn.coollabs.io/coolify/install.sh | sudo bash
    

-   **Proceso Automático:** El script comenzará a descargar e instalar automáticamente **Docker**, **Docker Compose** y todos los componentes internos de Coolify, lo que tardará unos minutos.
    

----------

### 6. Configuración Inicial por Interfaz Web

Al finalizar la instalación, Coolify estará listo para ser configurado desde tu navegador:

-   **Acceder al Asistente:** Abre tu navegador y accede a la IP pública de tu VM, incluyendo el puerto **8000**:

    http://<IP_PUBLICA_AZURE>:8000
    
-   **Crear Usuario:** Serás redirigido a la página de registro donde debes crear tu **usuario administrador** (correo y contraseña) para la interfaz de Coolify.
    
-   **Finalizar Onboarding:** Completa o salta los pasos del asistente de configuración (Onboarding).

### ## 6. Configuración de Nombre DNS de Azure y HTTPS 🔒

El objetivo de este paso es obtener una dirección más amigable y habilitar el certificado SSL (HTTPS) de forma gratuita.

### 6.1. Configurar la Etiqueta de Nombre DNS en Azure

1.  **Ir a la VM:** En el Portal de Azure, ve a la configuración de tu **Máquina Virtual**
    
2.  **Buscar DNS:** En la sección de Información esencial (_Essentials_), haz clic en el elemento **Dns Name** (Nombre DNS).
    
3.  **Ir a Configuración:** En la página de la **IP pública**, navega a **Configuración**.
    
4.  **Asignar Etiqueta:** En el campo **Etiqueta de nombre DNS** (_DNS name label_), introduce un nombre que sea **único a nivel global** en esa región de Azure 
    
5.  **Guardar:** Haz clic en **Guardar** y obtendrás una nueva dirección de acceso temporal.
    

----------

### 6.2. Configurar HTTPS en Coolify

1.  **Acceder al Panel:** Entra al panel de Coolify usando la IP pública y el puerto temporal: http://<IP_PUBLICA_AZURE>:8000.
    
2.  **Ir a Ajustes:** Dentro de Coolify, ve al menú lateral y navega a **Settings** , **Domains** (Ajustes - Dominios).
    
3.  **Agregar Dominio:** En la pestaña **General**, ingresa tu nueva URL completa con HTTPS en el campo _Domain_ 
    
4.  **Guardar:** Haz clic en **Guardar**.
    
5.  **Generación de SSL:** Coolify solicitará y generará automáticamente un **certificado SSL** con Let’s Encrypt para esta dirección.
    
    -   Asegúrate de que los puertos **80** y **443** sigan abiertos en tu NSG de Azure para esta verificación.
        
6.  **Verificación Final:** Accede usando la dirección segura y deberías ver el panel principal con la conexión segura activa.

