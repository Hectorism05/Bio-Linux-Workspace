# 🔋 Módulo 02: Optimización de Energía y Batería

Uno de los mayores retos al usar Linux en una laptop es la duración de la batería. Por defecto, el sistema no siempre viene configurado para aprovechar al máximo el ahorro de energía de los procesadores modernos (como la serie Ryzen de AMD).

En este módulo configuraremos un entorno de gestión de energía eficiente, priorizando la vida útil de la batería a largo plazo y la salud física de la celda.

## 🛠️ Herramientas a utilizar
* **TLP:** Gestor de energía avanzado en segundo plano (Recomendado para cualquier distribución).
* **Powertop:** Herramienta de monitoreo y diagnóstico de consumo (Desarrollada por Intel, pero funcional en procesadores AMD).
* **Controladores ACPI:** Para gestión directa de umbrales a nivel de hardware.

---

## 1. Configuración de Umbrales de Carga (Específico Lenovo IdeaPad)
Para evitar la degradación de la batería por mantenerla siempre al 100%, activaremos el modo de conservación. Esto detiene la carga al alcanzar ~55-60% (el tope exacto lo decide el firmware). Ideal si trabajas conectado a la corriente la mayor parte del tiempo.

> **Nota importante sobre umbrales:**
> En los Lenovo IdeaPad, el modo conservación es binario (activado/desactivado). No permite configurar porcentajes exactos como 20% o 60%. Tampoco tiene un umbral inferior de inicio (puedes cargar desde 0% hasta el tope sin problema).
> Si tu equipo es un ThinkPad u otra marca (Dell, HP, Asus), los parámetros serán diferentes (normalmente sí permiten porcentajes personalizables).

**Activar el modo conservación (carga máxima ~55-60%):**
\`\`\`bash
sudo sh -c "echo 1 > /sys/bus/platform/drivers/ideapad_acpi/VPC2004:00/conservation_mode"
\`\`\`

**Desactivar el modo (cargar hasta 100%):**
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
\`\`\`
*(Nota: `sudo tlp start` es redundante porque `systemctl start tlp` ya lo ejecuta. Puedes omitirlo).*

**Verificar que TLP está activo:**
\`\`\`bash
sudo tlp-stat -s
\`\`\`

**Configurar persistencia del modo conservación en TLP:**
Para que el modo conservación se active automáticamente tras cada reinicio, edita el archivo de configuración:
\`\`\`bash
sudo nano /etc/tlp.conf
\`\`\`

Busca o añade la siguiente línea (valores válidos: 0 = desactivado, 1 = activado):
\`\`\`ini
STOP_CHARGE_THRESH_BAT0=1
\`\`\`

> ⚠️ **Importante para Lenovo IdeaPad:** A diferencia de los ThinkPad, aquí NO se usa `START_CHARGE_THRESH_BAT0` ni valores porcentuales como 60. Tu hardware solo acepta 0 o 1. Para ver qué parámetros soporta tu equipo, ejecuta: `sudo tlp-stat -b`

**Después de editar, reinicia TLP:**
\`\`\`bash
sudo systemctl restart tlp
\`\`\`

---

## 3. Monitoreo con Powertop
Usaremos Powertop exclusivamente para diagnosticar qué procesos o dispositivos consumen más energía en tiempo real.

**Instalación y ejecución:**
\`\`\`bash
sudo apt install powertop
sudo powertop
\`\`\`

> **Importante:** No utilices la función `--auto-tune` de Powertop si ya tienes TLP activo, ya que ambos servicios podrían pisar sus configuraciones mutuamente. Úsalo solo como herramienta de diagnóstico.

---

## 🤝 Contribuciones
¿Lograste configurar los umbrales de carga en una laptop de otra marca (Dell, HP, Asus) o en un Lenovo ThinkPad? ¡Me encantaría saber cómo lo hiciste! Siéntete libre de abrir un **Issue** o enviar un **Pull Request** para documentarlo aquí y que este recurso sea útil para más estudiantes.
