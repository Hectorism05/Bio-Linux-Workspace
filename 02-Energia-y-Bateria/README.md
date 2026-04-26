# 🔋 Módulo 02: Optimización de Energía y Batería

Uno de los mayores retos al usar Linux en una laptop es la duración de la batería. Por defecto, el sistema no siempre viene configurado para aprovechar al máximo el ahorro de energía de los procesadores modernos (como la serie Ryzen de AMD).

En este módulo configuraremos un entorno de gestión de energía eficiente, priorizando la vida útil de la batería a largo plazo y la salud física de la celda.

## 🛠️ Herramientas a utilizar
* **TLP:** Gestor de energía avanzado en segundo plano (Recomendado para cualquier distribución).
* **Powertop:** Herramienta de monitoreo y diagnóstico de consumo (Desarrollada por Intel, pero funcional en procesadores AMD).
* **Controladores ACPI:** Para gestión directa de umbrales a nivel de hardware.

---

## 1. Configuración de Umbrales de Carga (Específico Lenovo)
Para evitar la degradación de la batería por mantenerla siempre al 100%, activaremos el modo de conservación. Esto limitará la carga entre un 20% y un 60%, ideal si trabajas conectado a la corriente la mayor parte del tiempo.

> **Nota:** Esta ruta de archivo es específica para la serie Lenovo IdeaPad. Si utilizas otra marca, el controlador ACPI podría variar o deberás gestionar los umbrales directamente desde la configuración de TLP.

**Activar el límite de carga (60%):**
\`\`\`bash
sudo sh -c "echo 1 > /sys/bus/platform/drivers/ideapad_acpi/VPC2004:00/conservation_mode"
\`\`\`

**Desactivar el límite (Cargar al 100%):**
\`\`\`bash
sudo sh -c "echo 0 > /sys/bus/platform/drivers/ideapad_acpi/VPC2004:00/conservation_mode"
\`\`\`

---

## 2. Instalación y Activación de TLP
TLP aplica reglas automáticas para reducir el consumo del CPU y periféricos (Wi-Fi, Bluetooth, Audio) cuando se detecta el uso de batería.

**Instalación:**
\`\`\`bash
sudo apt update && sudo apt install tlp tlp-rdw
\`\`\`

**Activación del servicio:**
\`\`\`bash
sudo systemctl enable tlp
sudo systemctl start tlp
sudo tlp start
\`\`\`

**Ajuste de umbrales persistentes:**
Para que los límites de 20% y 60% se mantengan tras reiniciar, editaremos el archivo de configuración:
\`\`\`bash
sudo nano /etc/tlp.conf
\`\`\`
*(Busca y ajusta las siguientes líneas):*
\`\`\`ini
START_CHARGE_THRESH_BAT0=20
STOP_CHARGE_THRESH_BAT0=60
\`\`\`

---

## 3. Monitoreo con Powertop
Usaremos Powertop exclusivamente para diagnosticar qué procesos o dispositivos consumen más energía en tiempo real.

**Instalación y ejecución:**
\`\`\`bash
sudo apt install powertop
sudo powertop
\`\`\`

> **Importante:** No utilices la función `--auto-tune` de Powertop si ya tienes TLP activo, ya que ambos servicios podrían entrar en conflicto. Úsalo solo como herramienta de diagnóstico.

---

## 🤝 Contribuciones
¿Lograste configurar los umbrales de carga en una laptop de otra marca (Dell, HP, Asus)? ¡Me encantaría saber cómo lo hiciste! Siéntete libre de abrir un **Issue** o enviar un **Pull Request** para documentarlo aquí y que este recurso sea útil para más estudiantes.
