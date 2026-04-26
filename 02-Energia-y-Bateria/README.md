# 🔋 Módulo 02: Optimización de Energía y Batería

Uno de los mayores retos al usar Linux en una laptop es la duración de la batería. Por defecto, el sistema no siempre viene configurado para aprovechar al máximo el ahorro de energía de los procesadores modernos (como la serie Ryzen de AMD). 

En este módulo configuraremos un entorno de gestión de energía eficiente, priorizando la vida útil de la batería a largo plazo.

## 🛠️ Herramientas a utilizar
* **TLP:** Gestor de energía avanzado en segundo plano.
* **Powertop:** Herramienta de monitoreo y diagnóstico de consumo de Intel.
* **Controladores ACPI:** Para gestión directa de umbrales a nivel de hardware (Específico para Lenovo).

---

## 1. Configuración de Umbrales de Carga (Lenovo IdeaPad)
Para evitar la degradación de la batería por mantenerla siempre al 100%, activaremos el modo de conservación. Esto limitará la carga entre un 20% y un 60%, ideal si usas la laptop conectada a la corriente la mayor parte del tiempo.

**Activar el límite de carga (60%):**
\`\`\`bash
sudo sh -c "echo 1 > /sys/bus/platform/drivers/ideapad_acpi/VPC2004:00/conservation_mode"
\`\`\`

**Desactivar el límite (Cargar al 100% para viajes):**
\`\`\`bash
sudo sh -c "echo 0 > /sys/bus/platform/drivers/ideapad_acpi/VPC2004:00/conservation_mode"
\`\`\`

---

## 2. Instalación y Activación de TLP
TLP aplicará reglas de ahorro de energía automáticamente (como apagar el Wi-Fi o Bluetooth si no se usan, y reducir el consumo del CPU en modo batería).

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

**Ajuste de umbrales en TLP (Permanente):**
Para que los límites de 20% y 60% sean persistentes, editaremos el archivo de configuración:
\`\`\`bash
sudo nano /etc/tlp.conf
\`\`\`
*(Busca y descomenta las siguientes líneas, ajustando los valores):*
\`\`\`ini
START_CHARGE_THRESH_BAT0=20
STOP_CHARGE_THRESH_BAT0=60
\`\`\`

---

## 3. Monitoreo con Powertop
Mientras TLP hace el trabajo en segundo plano, usaremos Powertop únicamente para diagnosticar qué procesos están consumiendo más energía en tiempo real.

**Instalación y ejecución:**
\`\`\`bash
sudo apt install powertop
sudo powertop
\`\`\`
*(Nota: No uses Powertop para aplicar ajustes de energía si ya tienes TLP activo, ya que pueden entrar en conflicto. Úsalo solo para monitorear).*
