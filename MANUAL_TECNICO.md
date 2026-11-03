# 🛠️ Manual Técnico · Manicura & Lash Spa

**Autor:** Juan & Antigravity  
**Stack:** HTML5, Tailwind CSS (CDN), Vanilla JavaScript (ES6+), LocalStorage, QRCode.js (con fallback QuickChart.io), Canvas Confetti.  
**Despliegue:** GitHub Pages (`https://juancdgo.github.io/manucura/`).  
**Copia de Seguridad:** Google Drive (`Mini Apps/manucura`).

---

## 1. Arquitectura y Componentes

La aplicación funciona como una SPA autocontenida con 3 vistas conmutables:
* **Vista 1 (Panel Administradora):** Gestión de clientas, fichas técnicas, recordatorios WhatsApp, control de saldo y configuración del estudio.
* **Vista 2 (Turnero Online para Clientas):** Activada con `?booking=true`, permite a la clienta solicitar turnos según días y horarios laborales del salón.
* **Vista 3 (Tarjeta Digital de Fidelidad):** Activada con `?pass=ID_CLIENTA` o `?card=ID_CLIENTA`, vista pública aislada y segura para la clienta con barra de progreso, sellos temáticos (esmaltes 💅) y código QR para mostrador.

---

## 2. Esquema de Persistencia (LocalStorage)

### 1. `manucura_settings` (Object)
```typescript
{
  salonName: string;          // "Nails · Studio Sofía"
  salonLogo: string;          // Base64 Data URL (optimizado a 280px via Canvas)
  salonPhone: string;         // WhatsApp del estudio
  alias: string;              // Alias de cobro MP/MODO
  cbu: string;                // CBU/CVU para transferencias
  titular: string;            // Nombre del titular de la cuenta
  targetStamps: number;       // Meta de sellos (default: 6)
  rewardDescription: string;  // "1 Service de Semipermanente Gratis"
  template: string;           // Plantilla de recordatorio WhatsApp
  bookingEnabled: boolean;    // Estado del turnero online
  workDays: number[];         // Días laborales [1, 2, 3, 4, 5, 6]
  bookingStartHour: number;   // 9
  bookingEndHour: number;     // 20
}
```

### 2. `manucura_clients` (Array<Object>)
```typescript
{
  id: string;                 // "c-1", "c-179374..."
  name: string;               // "Valentina Rossi"
  phone: string;              // "5491144229988"
  service: string;            // "Semipermanente", "Kapping Gel", etc.
  nailShape: string;          // "Almendra", "Cuadrada", "Coffin", etc.
  color: string;              // "Vía Láctea con glitters"
  lastVisit: string;          // "YYYY-MM-DD"
  intervalWeeks: number;      // 3
  debt: number;               // ARS $ (0 si está al día)
  stamps: number;             // Sellos acumulados (0..N)
  notes: string;              // Ficha técnica (cutículas, alergias, tips)
  lastReminderSent: string;   // ISO string de último recordatorio
}
```

---

## 3. Integración de Cobro QR Infalible

Se implementa el estándar con doble motor:
```javascript
let success = false;
try {
  if (typeof QRCode !== 'undefined') {
    new QRCode(container, { text: qrPayload, width: 170, height: 170, colorDark: '#881337', colorLight: '#ffffff' });
    success = true;
  }
} catch (e) { console.warn(e); }
if (!success) {
  container.innerHTML = '<img src="https://quickchart.io/qr?text=' + encodeURIComponent(qrPayload) + '&size=170" alt="QR Cobro" class="w-[170px] h-[170px] rounded-xl shadow-sm mx-auto block">';
}
```

---

## 4. Despliegue y Mantenimiento

* Repositorio: `https://github.com/JuanCDgo/manucura.git`
* Rama principal: `main`
* Despliegue automático vía GitHub Pages sobre `/root`.
