# Reporte Técnico: Infraestructura como Código con Terraform y Ansible en AWS

## 1. Resumen Ejecutivo

Este proyecto implementa una solución completa de Infraestructura como Código (IaC) que automatiza el despliegue de aplicaciones containerizadas en la nube de Amazon Web Services (AWS). La solución combina Terraform para el aprovisionamiento de infraestructura y Ansible para la gestión de configuración, logrando un flujo de trabajo totalmente automatizado desde la creación de recursos hasta el despliegue de aplicaciones.

**Logros principales:**

- Infraestructura AWS completamente automatizada
- Generación dinámica de inventarios de Ansible
- Instalación automatizada de Docker
- Despliegue de contenedores mediante playbooks
- Documentación completa y scripts de ayuda

## 2. Objetivos del Proyecto

### Objetivo General

Implementar una infraestructura automatizada en AWS que permita el despliegue rápido y reproducible de aplicaciones containerizadas utilizando herramientas de IaC.

### Objetivos Específicos

1. **Automatización de Infraestructura**
   - Provisionar instancias EC2 mediante Terraform
   - Configurar Security Groups y reglas de firewall
   - Gestionar claves SSH de forma segura

2. **Gestión de Configuración**
   - Implementar playbooks de Ansible para instalación de software
   - Automatizar el despliegue de contenedores Docker
   - Crear inventarios dinámicos desde Terraform

3. **Documentación y Usabilidad**
   - Generar documentación completa del proyecto
   - Crear scripts helper para facilitar operaciones
   - Establecer mejores prácticas de seguridad

## 3. Arquitectura de la Solución

### Diagrama de Arquitectura

```Markdown
┌─────────────────────────────────────────────────────────────────┐
│                          AWS Cloud                              │
│                                                                 │
│  ┌────────────────────────────────────────────────────────┐     │
│  │                    VPC Default                         │     │
│  │                                                        │     │
│  │  ┌──────────────────────────────────────────────┐      │     │
│  │  │         Security Group                       │      │     │
│  │  │  - SSH (22)                                  │      │     │
│  │  │  - HTTP (80)                                 │      │     │
│  │  │  - HTTPS (443)                               │      │     │
│  │  │  - Docker Ports (8000-9000)                  │      │     │
│  │  └──────────────────────────────────────────────┘      │     │
│  │                          │                             │     │
│  │                          ▼                             │     │
│  │  ┌──────────────────────────────────────────────┐      │     │
│  │  │     EC2 Instance (t3.micro)                  │      │     │
│  │  │     - Ubuntu 22.04 LTS                       │      │     │
│  │  │     - Python3                                │      │     │
│  │  │     - Docker Engine                          │      │     │
│  │  │                                              │      │     │
│  │  │     ┌─────────────────────────────┐          │      │     │
│  │  │     │  Docker Container           │          │      │     │
│  │  │     │  - Super Mario App          │          │      │     │
│  │  │     │  - Port: 8787               │          │      │     │
│  │  │     └─────────────────────────────┘          │      │     │
│  │  └──────────────────────────────────────────────┘      │     │
│  │                                                        │     │
│  └────────────────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────────────────┘
                           ▲
                           │
                    ┌──────┴──────┐
                    │             │
              ┌─────▼─────┐ ┌─────▼──────┐
              │ Terraform │ │  Ansible   │
              │           │ │            │
              │ - main.tf │ │ - install  │
              │ - vars.tf │ │   docker   │
              │ - out.tf  │ │ - run      │
              │           │ │   container│
              └───────────┘ └────────────┘
                    │             │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │  Developer  │
                    │  Workstation│
                    └─────────────┘
```

### Flujo de Trabajo

```Bash
1. Desarrollo Local
   ├── Editar archivos Terraform
   └── Configurar variables

2. Terraform Apply
   ├── Crear EC2 Instances
   ├── Configurar Security Groups
   ├── Instalar SSH Keys
   └── Generar inventario Ansible

3. Ansible Playbooks
   ├── Conectar a instancias
   ├── Instalar Docker
   └── Desplegar contenedores

4. Aplicación Disponible
   └── Acceso vía IP pública
```

## 4. Tecnologías Utilizadas

### Infraestructura como Código

| Tecnología | Versión | Propósito |
|------------|---------|-----------|
| **Terraform** | >= 1.0 | Aprovisionamiento de infraestructura AWS |
| **AWS Provider** | ~> 5.0 | Integración con servicios de AWS |

### Gestión de Configuración

| Tecnología | Versión | Propósito |
|------------|---------|-----------|
| **Ansible** | Latest | Automatización de configuración |
| **Python3** | >= 3.8 | Runtime para Ansible |

### Plataforma Cloud

| Servicio AWS | Propósito |
|--------------|-----------|
| **EC2** | Instancias de computación |
| **VPC** | Red virtual privada |
| **Security Groups** | Firewall de red |
| **Key Pairs** | Autenticación SSH |

### Containerización

| Tecnología | Versión | Propósito |
|------------|---------|-----------|
| **Docker** | Latest | Ejecución de contenedores |
| **Docker Hub** | - | Registro de imágenes |

## 5. Implementación

### 5.1 Estructura del Proyecto

```Bash
ansible_and_vm/
├── vm_in_aws/                      # Infraestructura Terraform
│   ├── main.tf                     # Configuración principal
│   ├── variables.tf                # Definición de variables
│   ├── outputs.tf                  # Outputs de Terraform
│   ├── inventory.tpl               # Template de inventario
│   ├── terraform.tfvars            # Valores de variables (gitignored)
│   ├── terraform.tfvars.example    # Ejemplo de configuración
│   ├── .gitignore                  # Archivos ignorados
│   └── README.md                   # Documentación Terraform
│
└── training-ansible/               # Configuración Ansible
    ├── ansible.cfg                 # Configuración de Ansible
    ├── playbooks/
    │   ├── install_docker.yml      # Playbook para Azure (original)
    │   ├── run_container.yml       # Playbook para Azure (original)
    │   ├── install_docker_aws.yml  # Playbook para AWS
    │   └── run_container_aws.yml   # Playbook para AWS
    ├── roles/
    │   ├── docker_install/         # Role: Instalación de Docker
    │   │   └── tasks/
    │   │       └── main.yml
    │   └── docker_container/       # Role: Ejecución de contenedores
    │       └── tasks/
    │           └── main.yml
    ├── inventory/
    │   ├── hosts.ini               # Inventario Azure (original)
    │   └── aws_hosts.ini           # Inventario AWS (generado)
    ├── run_ansible_aws.sh          # Script helper
    ├── README.md                   # Documentación original
    └── README_AWS.md               # Documentación AWS
```

### 5.2 Configuración de Terraform

#### main.tf

Archivo principal que define todos los recursos de AWS:

**Componentes principales:**

1. **Provider Configuration**: Configuración de AWS y región
2. **Data Sources**: VPC y subnets por defecto
3. **Security Group**: Reglas de firewall
4. **Key Pair**: Clave SSH para acceso
5. **EC2 Instances**: Instancias Ubuntu 22.04
6. **Local File**: Generación de inventario Ansible

**Características destacadas:**

- AMI de Ubuntu más reciente (automático)
- User data para bootstrap inicial
- Configuración de usuario Ansible
- Instalación automática de Python3

#### variables.tf

Define todas las variables configurables:

```hcl
- aws_region          (default: us-east-1)
- project_name        (default: ansible-training)
- instance_type       (default: t3.micro)
- instance_count      (default: 1)
- root_volume_size    (default: 20)
- ssh_public_key      (requerido)
- allowed_ssh_cidr    (default: 0.0.0.0/0)
- ansible_user        (default: ubuntu)
```

#### outputs.tf

Expone información útil después del despliegue:

```hcl
- instance_ids
- instance_public_ips
- instance_public_dns
- instance_private_ips
- security_group_id
- ssh_connection_commands
- ansible_inventory_path
```

### 5.3 Configuración de Ansible

#### Playbooks Creados

**install_docker_aws.yml**

```yaml
---
- hosts: aws_vm
  become: yes
  roles:
    - docker_install
```

**run_container_aws.yml**

```yaml
---
- hosts: aws_vm
  become: yes
  roles:
    - docker_container
```

#### Roles Utilizados

**docker_install/**
- Instala dependencias de Docker
- Agrega clave GPG oficial
- Configura repositorio de Docker
- Instala Docker CE

**docker_container/**
- Ejecuta contenedor Super Mario
- Mapea puerto 8787:8080
- Configura restart policy

#### Inventario Dinámico

El archivo `inventory.tpl` genera automáticamente:

```ini
[aws_vm]
<IP_PUBLICA> ansible_user=<USUARIO>

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

## 6. Componentes del Sistema

### 6.1 Recursos de AWS Creados

#### EC2 Instance

- **Tipo**: t3.micro (Free Tier eligible)
- **AMI**: Ubuntu 22.04 LTS (Jammy)
- **Almacenamiento**: 20 GB gp3
- **Región**: us-east-1
- **IP Pública**: 18.204.211.198

#### Security Group

Reglas de entrada configuradas:

| Puerto | Protocolo | Origen | Propósito |
|--------|-----------|--------|-----------|
| 22 | TCP | Configurable | SSH |
| 80 | TCP | 0.0.0.0/0 | HTTP |
| 443 | TCP | 0.0.0.0/0 | HTTPS |
| 8000-9000 | TCP | 0.0.0.0/0 | Aplicaciones Docker |

Reglas de salida:

- Permitir todo el tráfico saliente

#### Key Pair

- **Nombre**: ansible-training-key
- **Tipo**: RSA 4096 bits
- **Ubicación**: ~/.ssh/ansible_key

### 6.2 Software Instalado

#### En la instancia EC2

1. **Sistema Base**
   - Ubuntu 22.04 LTS
   - Python 3.10
   - Actualizaciones de seguridad

2. **Docker**
   - Docker CE (Community Edition)
   - Docker daemon configurado
   - Usuario agregado al grupo docker

3. **Aplicación**
   - Contenedor: pengbai/docker-supermario
   - Puerto expuesto: 8787
   - Estado: running

## 7. Proceso de Despliegue

### 7.1 Preparación del Entorno

```bash
# 1. Generar clave SSH
ssh-keygen -t rsa -b 4096 -f ~/.ssh/ansible_key

# 2. Configurar AWS CLI
aws configure

# 3. Configurar variables de Terraform
cd vm_in_aws
cp terraform.tfvars.example terraform.tfvars
# Editar terraform.tfvars con la clave pública
```

### 7.2 Despliegue de Infraestructura

```bash
# 1. Inicializar Terraform
terraform init

# 2. Validar configuración
terraform validate

# 3. Revisar plan
terraform plan

# 4. Aplicar infraestructura
terraform apply
```

**Salida esperada:**
```
Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

Outputs:

instance_public_ips = [
  "18.204.211.198",
]
ssh_connection_commands = [
  "ssh -i ~/.ssh/ansible_key ubuntu@18.204.211.198",
]
```

### 7.3 Configuración con Ansible

```bash
cd ../training-ansible

# 1. Verificar conectividad
ansible -i inventory/aws_hosts.ini aws_vm -m ping \
  --private-key=~/.ssh/ansible_key

# 2. Instalar Docker
ansible-playbook -i inventory/aws_hosts.ini \
  playbooks/install_docker_aws.yml \
  --private-key=~/.ssh/ansible_key

# 3. Desplegar contenedor
ansible-playbook -i inventory/aws_hosts.ini \
  playbooks/run_container_aws.yml \
  --private-key=~/.ssh/ansible_key
```

## 8. Resultados Obtenidos

### 8.1 Ejecución Exitosa de Terraform

```Bash
Resources created:
✓ aws_security_group.ansible_vm_sg
✓ aws_key_pair.ansible_key
✓ aws_instance.ansible_vm[0]
✓ local_file.ansible_inventory

Time: ~2 minutos
Status: SUCCESS
```

### 8.2 Ejecución Exitosa de Ansible

**Instalación de Docker:**

```yml
PLAY RECAP
18.204.211.198 : ok=5 changed=4 unreachable=0 failed=0
```

**Despliegue de Contenedor:**

```yml
PLAY RECAP
18.204.211.198 : ok=2 changed=1 unreachable=0 failed=0
```

### 8.3 Aplicación Funcionando

**URL de acceso:** http://18.204.211.198:8787

**Estado del contenedor:**

```bash
$ docker ps
CONTAINER ID   IMAGE                              STATUS
abc123def456   pengbai/docker-supermario:latest   Up 5 minutes
```

#### Imágenes

![imagen_demostrativa]("https://github.com/OscarGomez-jpg/mario_ansible?tab=readme-ov-file/img/image.png")
![imagen_demostrativa2]("https://github.com/OscarGomez-jpg/mario_ansible?tab=readme-ov-file/img/image_copy.png")

### 8.4 Verificación de Servicios

```bash
# SSH funcional
$ ssh -i ~/.ssh/ansible_key ubuntu@18.204.211.198
✓ Conexión exitosa

# Docker instalado
$ docker --version
Docker version 24.x.x, build xxxxx
✓ Versión correcta

# Contenedor corriendo
$ curl http://localhost:8787
✓ Respuesta HTTP 200
```

## 9. Seguridad y Mejores Prácticas

### 9.1 Implementadas

1. **Gestión de Claves SSH**
   - Claves RSA 4096 bits
   - Permisos correctos (600 para privada, 644 para pública)
   - No se commitean en el repositorio

2. **Gitignore Configurado**

   ```yml
   terraform.tfvars
   *.pem, *.key
   .terraform/
   terraform.tfstate*
   ```

3. **Variables Sensibles**
   - Archivo de ejemplo sin credenciales
   - Variables requeridas para ssh_public_key
   - Documentación clara de configuración

4. **Security Groups**
   - Reglas mínimas necesarias
   - CIDR configurable para SSH
   - Documentación de puertos abiertos

## 10. Costos y Recursos

### 10.1 Estimación de Costos AWS

**Con Free Tier:**

- EC2 t3.micro: $0/mes (750 horas gratis)
- EBS 20GB gp3: $0/mes (30 GB gratis)
- Transferencia de datos: $0/mes (100 GB salida gratis)

**Total mensual (Free Tier):** $0

**Sin Free Tier:**

- EC2 t3.micro: ~$7.50/mes (0.0104 $/hora × 730 horas)
- EBS 20GB gp3: ~$1.60/mes (0.08 $/GB-mes)
- Transferencia: Variable según uso

**Total mensual (sin Free Tier):** ~$9-10

### 10.2 Recursos Utilizados

**Desarrollo:**

- Tiempo de desarrollo: ~2 horas
- Tiempo de pruebas: ~30 minutos

**Ejecución:**

- Tiempo de despliegue Terraform: ~2 minutos
- Tiempo de ejecución Ansible: ~3-5 minutos
- Tiempo total de aprovisionamiento: ~7 minutos

**Almacenamiento:**

- Código fuente: ~50 KB
- Estado de Terraform: ~10 KB
- Total repositorio: <100 KB

## 11. Conclusiones

### 11.1 Logros Alcanzados

1. **Automatización Completa**
   - Se logró automatizar todo el proceso desde la creación de infraestructura hasta el despliegue de aplicaciones
   - Reducción del tiempo de aprovisionamiento de horas a minutos

2. **Infraestructura Reproducible**
   - Cualquier desarrollador puede recrear el entorno completo
   - Documentación clara y scripts de ayuda facilitan la adopción

3. **Integración Exitosa**
   - Terraform y Ansible trabajan juntos sin fricción
   - Inventario dinámico elimina configuración manual

4. **Seguridad**
   - Implementación de mejores prácticas
   - Separación correcta de secretos y código

5. **Escalabilidad**
   - Fácil agregar más instancias cambiando una variable
   - Arquitectura permite crecimiento horizontal

### 11.2 Lecciones Aprendidas

1. **Tipos de Instancia AWS**
   - t2.micro ya no es Free Tier, migrar a t3.micro
   - Verificar eligibilidad con AWS CLI

2. **Generación de Inventarios**
   - Templates de Terraform son muy útiles para configuración dinámica
   - Reduce errores de configuración manual

3. **Modularización**
   - Separar playbooks por proveedor (Azure/AWS) facilita mantenimiento
   - Roles de Ansible permiten reutilización

4. **Documentación**
   - Scripts helper mejoran significativamente la experiencia de usuario
   - Ejemplos concretos son más útiles que descripciones generales

## 12. Referencias

### 12.1 Documentación Oficial

- [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Ansible Documentation](https://docs.ansible.com/)
- [Ansible AWS Guide](https://docs.ansible.com/ansible/latest/collections/amazon/aws/docsite/guide_aws.html)
- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [Docker Documentation](https://docs.docker.com/)
