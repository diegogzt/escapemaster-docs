# 🚀 Guía de Despliegue a Producción

Esta guía explica cómo enviar los cambios de **master.escapemaster.es** y **marketplace.escapemaster.es** a producción.

## 📋 Arquitectura de Despliegue

```
┌─────────────────────────────────────────────────────────────┐
│                    Tu Máquina Local                      │
│  /Users/dgtovar/work/                                │
│  ├── apps/master-escapemaster/                        │
│  └── apps/marketplace/                                 │
└────────────────────┬────────────────────────────────────────┘
                     │ git push
                     ▼
┌─────────────────────────────────────────────────────────────┐
│                    GitHub                              │
│  diegogzt/master-escapemaster (repo)               │
└────────────────────┬────────────────────────────────────────┘
                     │ webhook
                     ▼
┌─────────────────────────────────────────────────────────────┐
│                    Vercel                              │
│  • master-escapemaster-em3pxglw8-dgtovar.vercel.app │
│  • escapemaster-rooms.vercel.app                      │
└────────────────────┬────────────────────────────────────────┘
                     │ proxy
                     ▼
┌─────────────────────────────────────────────────────────────┐
│              Servidor (5.75.249.177)                   │
│  • Nginx (reverse proxy)                              │
│  • master.escapemaster.es → Vercel                    │
│  • marketplace.escapemaster.es → Vercel                │
└─────────────────────────────────────────────────────────────┘
```

## 🛠️ Scripts de Despliegue

He creado 3 scripts para automatizar el proceso de despliegue:

### 1. `deploy-production.sh` - Despliegue Básico

Script simple que:

- Verifica cambios pendientes
- Crea commit
- Hace push a GitHub
- Vercel despliega automáticamente

**Uso:**

```bash
cd /Users/dgtovar/work
./deploy-production.sh
```

### 2. `ssh-server.sh` - Gestión del Servidor

Script para conectarse al servidor SSH y gestionar nginx:

- Ver estado de nginx
- Reiniciar nginx
- Recargar configuración
- Ver logs
- Editar configuraciones

**Uso:**

```bash
cd /Users/dgtovar/work
./ssh-server.sh
```

### 3. `deploy-full.sh` - Despliegue Completo ⭐

Script completo que incluye:

1. Verificar cambios de Git
2. Crear commit y hacer push
3. Esperar despliegue de Vercel
4. Verificar que los sitios funcionan
5. Opciones adicionales del servidor

**Uso:**

```bash
cd /Users/dgtovar/work
./deploy-full.sh
```

## 📝 Proceso de Despliegue Paso a Paso

### Opción 1: Despliegue Automático (Recomendado)

```bash
# 1. Navegar al directorio de trabajo
cd /Users/dgtovar/work

# 2. Ejecutar el script completo
./deploy-full.sh

# 3. Seguir las instrucciones del script
#    - Confirmar cambios
#    - Esperar despliegue de Vercel (2-3 min)
#    - Verificar que los sitios funcionan
#    - Opcional: Gestionar nginx
```

### Opción 2: Despliegue Manual

```bash
# 1. Verificar cambios
git status

# 2. Agregar cambios
git add .

# 3. Crear commit
git commit -m "Descripción de los cambios"

# 4. Hacer push a GitHub
git push origin main

# 5. Vercel despliega automáticamente
#    Espera 2-3 minutos

# 6. Verificar despliegue
#    Abre: https://master.escapemaster.es
#    Abre: https://marketplace.escapemaster.es
```

### Opción 3: Solo Gestión del Servidor

```bash
# 1. Ejecutar script de gestión
./ssh-server.sh

# 2. Seleccionar opción:
#    1) Ver estado de nginx
#    2) Reiniciar nginx
#    3) Recargar configuración
#    4) Ver logs
#    5-8) Ver/editar configuraciones
#    9) Probar configuración
#    10) Salir
```

## 🔗 URLs de Producción

| Aplicación      | URL Vercel                                               | URL Producción                      |
| --------------- | -------------------------------------------------------- | ----------------------------------- |
| **Master**      | https://master-escapemaster-em3pxglw8-dgtovar.vercel.app | https://master.escapemaster.es      |
| **Marketplace** | https://escapemaster-rooms.vercel.app                    | https://marketplace.escapemaster.es |
| **API**         | -                                                        | https://api.escapemaster.es         |

## 🔧 Configuración de Nginx

El servidor (5.75.249.177) usa nginx como reverse proxy:

### master.escapemaster.es

```nginx
server {
    server_name master.escapemaster.es;

    location / {
        proxy_pass https://master-escapemaster-em3pxglw8-dgtovar.vercel.app/;
        proxy_set_header Host master-escapemaster-em3pxglw8-dgtovar.vercel.app;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_ssl_server_name on;
    }
}
```

### marketplace.escapemaster.es

```nginx
server {
    server_name marketplace.escapemaster.es;

    location / {
        proxy_pass https://escapemaster-rooms.vercel.app/;
        proxy_set_header Host escapemaster-rooms.vercel.app;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_ssl_server_name on;
    }
}
```

## 🔑 Acceso SSH

**Comando directo:**

```bash
ssh -i /Users/dgtovar/.ssh/introprg root@5.75.249.177
```

**Usando el script:**

```bash
./ssh-server.sh
# Seleccionar opción 4) Conectar al servidor
```

## 📊 Monitoreo de Despliegue

### Vercel Dashboard

- URL: https://vercel.com/dashboard
- Proyectos:
  - master-escapemaster
  - escapemaster-rooms (marketplace)

### Logs del Servidor

```bash
# Ver logs de nginx
ssh -i /Users/dgtovar/.ssh/introprg root@5.75.249.177 "tail -n 50 /var/log/nginx/error.log"

# Ver estado de nginx
ssh -i /Users/dgtovar/.ssh/introprg root@5.75.249.177 "systemctl status nginx"
```

## ⚠️ Solución de Problemas

### El despliegue falló en Vercel

1. Ve al dashboard de Vercel
2. Busca el proyecto (master-escapemaster o escapemaster-rooms)
3. Revisa los logs de build
4. Corrige el error localmente
5. Haz push de nuevo

### El sitio no carga después del despliegue

1. Verifica que Vercel haya desplegado correctamente
2. Conecta al servidor SSH
3. Recarga nginx: `nginx -s reload`
4. Verifica logs de nginx
5. Prueba la configuración: `nginx -t`

### Error de conexión SSH

1. Verifica que la clave SSH existe: `ls -la /Users/dgtovar/.ssh/introprg`
2. Verifica permisos: `chmod 600 /Users/dgtovar/.ssh/introprg`
3. Prueba conexión: `ssh -v -i /Users/dgtovar/.ssh/introprg root@5.75.249.177`

## 🎯 Checklist de Despliegue

Antes de desplegar:

- [ ] Todos los cambios están commitados
- [ ] El código compila localmente (`npm run build`)
- [ ] Las pruebas pasan
- [ ] Los cambios están documentados

Después de desplegar:

- [ ] Vercel muestra despliegue exitoso
- [ ] master.escapemaster.es carga correctamente
- [ ] marketplace.escapemaster.es carga correctamente
- [ ] El color primario es `#0097b2`
- [ ] Las nuevas secciones del sidebar funcionan
- [ ] El campo `is_active` funciona en salas
- [ ] Los filtros de estado funcionan
- [ ] No hay errores en la consola del navegador

## 📚 Recursos Adicionales

- [Documentación de Vercel](https://vercel.com/docs)
- [Documentación de Nginx](https://nginx.org/en/docs/)
- [Workflow de Backend](/.agent/workflows/backend.md)
- [Workflow de Master](/.agent/workflows/master.md)
