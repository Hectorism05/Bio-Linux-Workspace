# 🔋 Módulo 02: Optimización de Energía y Batería

Uno de los mayores retos al usar Linux en una laptop es la duración de la batería. Por defecto, el sistema no siempre viene configurado para aprovechar al máximo el ahorro de energía de los procesadores modernos (como la serie Ryzen de AMD).

En este módulo configuraremos un entorno de gestión de energía eficiente, priorizando la vida útil de la batería a largo plazo y la salud física de la celda.

## 🛠️ Herramientas a utilizar

| Herramienta | Función Principal | Observaciones |
|-------------|-------------------|---------------|
| **TLP** | Gestor de energía avanzado en segundo plano. | Recomendado para cualquier distribución. |
| **Powertop** | Monitoreo y diagnóstico de consumo en tiempo real. | Desarrollada por Intel, pero funcional en AMD. |
| **Controladores ACPI** | Gestión directa de umbrales. | Actúa a nivel de hardware. |

---

## 1. Configuración de Umbrales de Carga (Específico Lenovo IdeaPad)

Para evitar la degradación de la batería por mantenerla siempre al 100%, activaremos el modo de conservación. Esto detiene la carga al alcanzar ~55-60% (el tope exacto lo decide el firmware). Es ideal si trabajas conectado a la corriente la mayor parte del tiempo.

> **ℹ️ Nota importante sobre umbrales:**
> En los Lenovo IdeaPad, el modo conservación es binario (activado/desactivado). No permite configurar porcentajes exactos como 20% o 60%, ni un umbral inferior de inicio.
> *Si tu equipo es un ThinkPad u otra marca (Dell, HP, Asus), los parámetros serán diferentes y normalmente sí permiten porcentajes personalizables.*

**Activar el modo conservación (carga máxima ~55-60%):**
```bash
sudo sh -c "echo 1 > /sys/bus/platform/drivers/ideapad_acpi/VPC2004:00/conservation_mode"
```

**Desactivar el modo (cargar hasta el 100%):**
```bash
sudo sh -c "echo 0 > /sys/bus/platform/drivers/ideapad_acpi/VPC2004:00/conservation_mode"
```

![Demostración de activación del modo conservación](img/01_modo_conservacion.png.jpg)

---

## 2. Instalación y Activación de TLP

TLP aplica reglas automáticas para reducir el consumo del CPU y periféricos (Wi-Fi, Bluetooth, Audio) cuando se detecta el uso de batería.

**Instalación:**
```bash
sudo apt update && sudo apt install tlp tlp-rdw
```

**Activación del servicio:**
```bash
sudo systemctl enable tlp
sudo systemctl start tlp
```
> **💡 Tip:** El comando `sudo tlp start` es redundante porque `systemctl start tlp` ya lo ejecuta. Puedes omitirlo.

**Verificar que TLP está activo:**
```bash
sudo tlp-stat -s
```

<details>
<summary>⚙️ <b>Configurar persistencia del modo conservación en TLP (Haz clic para expandir)</b></summary>

Para que el modo conservación se active automáticamente tras cada reinicio, edita el archivo de configuración:

```bash
sudo nano /etc/tlp.conf
```

Busca o añade la siguiente línea (valores válidos: `0
