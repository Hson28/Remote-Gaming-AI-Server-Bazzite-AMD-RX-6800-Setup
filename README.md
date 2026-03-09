# Remote Gaming & AI Server: Bazzite (AMD RX 6800) Setup

Denna guide beskriver konfigurationen av en högpresterande speldator körandes **Bazzite** (en immutable Linux-distro) för stabil fjärrstyrning och gaming över 5G-nätet. Dokumentet fungerar som en tutorial för att sätta upp **Sunshine**, **Tailscale**, **Wake-on-LAN** och lösa "headless"-problematik.

## 🛠 Hårdvara & Miljö
* **OS:** Bazzite (KDE Plasma / Wayland)
* **GPU:** AMD Radeon RX 6800
* **Router:** ASUS TUF-BE6500
* **Fjärranslutning:** Tailscale (VPN) & Moonlight (Klient)
* **Headless-fix:** DisplayPort Dummy Plug (DJY DOONJIEY)

---

## 1. BIOS & Fjärrstart (Wake-on-LAN)
För att väcka datorn på distans krävs konfiguration i moderkortets BIOS:

* **ErP Ready:** Ställ in på **Disabled**. Detta håller nätverkskortet strömsatt vid avstängning.
* **Power On By PCI-E:** Ställ in på **Enabled** för att tillåta väckning via "Magic Packets".
* **Restore AC Power Loss:** Ställ in på **Power On** (valfritt, men användbart om du använder ett smart eluttag för hård omstart).

---

## 2. Sunshine & Rättigheter (Immutable OS)
Bazzite är ett skrivskyddat (immutable) system, vilket innebär att vissa standardmetoder för Linux-behörigheter inte fungerar.

### Användargrupper
Istället för att ändra systemfiler, lägg till din användare i följande grupper för att ge Sunshine åtkomst till GPU-hårdvaran:
```bash
sudo usermod -aG video $USER
sudo usermod -aG render $USER# Remote Gaming & AI Server: Bazzite (AMD RX 6800) Setup

Denna guide beskriver hur man konfigurerar en kraftfull speldator körandes **Bazzite** (en immutable Linux-distro) för stabil fjärrstyrning och gaming över 5G-nätet. Den täcker konfiguration av **Sunshine**, **Tailscale**, **Wake-on-LAN** samt hur man löser "headless"-problem med en hårdvaru-dummy.

## 🛠 Hårdvara & Miljö
* **Operativsystem:** Bazzite (KDE Plasma / Wayland)
* **GPU:** AMD Radeon RX 6800
* **Router:** ASUS TUF-BE6500
* **Nätverk:** Tailscale (för säker 5G-tunnel)
* **Headless-lösning:** DisplayPort Dummy Plug

---

## 1. BIOS & Fjärrstart (Wake-on-LAN)
För att kunna starta datorn på distans krävs specifika inställningar i BIOS:

* **ErP Ready:** Ställ in på **Disabled**. Detta säkerställer att nätverkskortet har ström även när datorn är avstängd.
* **Power On By PCI-E:** Ställ in på **Enabled** för att tillåta routern att väcka datorn via ett "Magic Packet".
* **Restore AC Power Loss:** Ställ in på **Power On**. Om du använder ett smart uttag kan du tvinga datorn att starta genom att helt enkelt slå på strömmen.

---

## 2. Sunshine: Behörigheter & Grupper
Eftersom Bazzite är ett **immutable** (skrivskyddat) system kan man inte använda standardkommandon som `setcap` direkt på systemfiler. Lösningen är att hantera rättigheter via användargrupper.

### Lägg till användare i grupper
Kör följande kommandon för att ge Sunshine åtkomst till hårdvarukodning (GPU) och skärmdumpning utan root-behörighet:
```bash
sudo usermod -aG video $USER
sudo usermod -aG render $USER
