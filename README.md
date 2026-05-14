# ⬡ AuditWeb Toolkit

> Ecosistema modular de auditoría web y detección de amenazas.  
> Metodología técnica, firma digital RSA-SHA256, cifrado de datos y reportes listos para entrega profesional.

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white)
![Kali Linux](https://img.shields.io/badge/Kali_Linux-557C94?style=flat-square&logo=kalilinux&logoColor=white)
![WireGuard](https://img.shields.io/badge/WireGuard-88171A?style=flat-square&logo=wireguard&logoColor=white)
![Ollama](https://img.shields.io/badge/Ollama-AI_Local-black?style=flat-square)
![SQLite](https://img.shields.io/badge/SQLite+Fernet-003B57?style=flat-square&logo=sqlite&logoColor=white)
![License](https://img.shields.io/badge/Licencia-Privada-red?style=flat-square)

---

## ¿Qué hace?

AuditWeb es un toolkit de auditoría web modular desarrollado para entornos profesionales. Automatiza el ciclo completo de una auditoría: desde el reconocimiento pasivo hasta el reporte firmado digitalmente, sin enviar datos a servicios externos.

```
AUTORIZACIÓN  →  RECONOCIMIENTO  →  ANÁLISIS  →  ENTREGABLE
(RSA-SHA256)      (OSINT pasivo)    (activo)     (PDF firmado)
```

---

## Capacidades principales

### Reconocimiento pasivo (OSINT)
- Resolución DNS completa: MX, SPF, DMARC, NS
- WHOIS con detección de expiración y proveedor
- Wayback Machine — URLs históricas sensibles
- VirusTotal + AbuseIPDB — reputación de IP/dominio
- Detección de WAF y stack tecnológico

### Análisis estático de código (Whitebox)
- **Semgrep** — SAST sobre código fuente del cliente
- **Trufflehog** — detección de secretos y credenciales hardcodeadas
- Clasificación por severidad: ALTO / MEDIO / BAJO

### Escaneo dinámico
- **Nikto** — vulnerabilidades HTTP, headers, configuración
- **Nuclei** — templates selectivos: CVEs, misconfiguraciones, SSL/TLS

### Reportes y trazabilidad
- Reporte TXT/PDF firmado con **RSA-SHA256**
- Documento de autorización firmado digitalmente antes de cada auditoría
- Base de datos **SQLite + Fernet** (AES-128 CBC + HMAC) para hallazgos
- Historial auditable por cliente y proyecto

### IA local (sin nube)
- Integración con **Ollama** — análisis de hallazgos sin enviar datos a terceros
- Modelo personalizado con instrucciones anti-alucinación

---

## Demo — Output del scanner

Ejemplo real de salida (datos ficticios):

```
═══════════════════════════════════════════════════
  OBJETIVO: https://objetivo-cliente.com
═══════════════════════════════════════════════════

▶ Reconocimiento pasivo
  ✓ apex=objetivo-cliente.com | sub='www'
  ✓ DNS/WHOIS → MX: Google Workspace | SPF: pass | DMARC: ausente ⚠
  ✓ Wayback Machine → 12 URLs históricas sensibles detectadas
  ✓ WAF detectado: Cloudflare | Tech: Nginx 1.18, PHP 8.1
  ✓ VirusTotal → 0/94 engines | AbuseIPDB → score: 0

▶ Análisis de código fuente (Whitebox)
  ✓ Semgrep → 3 hallazgos — Alto:1 Medio:1 Bajo:1
  ✓ Trufflehog → 2 secretos detectados (1 crítico)

▶ Escaneo dinámico
  ✓ Nikto → 7 vulnerabilidades HTTP
  ✓ Nuclei → CVE-2023-XXXX (ALTO), SSL misconfiguration (MEDIO)

═══════════════════════════════════════════════════
  HALLAZGOS RESUMEN
  Alto: 3 | Medio: 4 | Bajo: 2 | Informativo: 5
═══════════════════════════════════════════════════

  Reporte firmado: auditoria_objetivo_cliente_20260514.txt
  Firma RSA-SHA256: a3f9b21c...
  Guardado en base de datos cifrada ✓
```

Ver reporte completo de ejemplo: [demo/sample_report.txt](demo/sample_report.txt)

---

## Stack tecnológico

| Componente | Tecnología |
|------------|-----------|
| Lenguaje | Python 3.10+ |
| Plataforma | Kali Linux |
| Reconocimiento | DNS, WHOIS, Wayback, VirusTotal, AbuseIPDB |
| SAST | Semgrep, Trufflehog |
| Escaneo dinámico | Nikto, Nuclei |
| IA local | Ollama (sin nube) |
| Firma digital | RSA-SHA256 (cryptography) |
| Base de datos | SQLite + Fernet (AES-128 CBC) |
| VPN | WireGuard con kill-switch kernel |

---

## Arquitectura (resumen)

```
audit_web.py (CLI principal)
│
├── commercial_recon.py      # OSINT: DNS, WAF, tech, Wayback, VirusTotal
├── modules/
│   ├── whitebox.py          # SAST: semgrep + trufflehog
│   ├── graybox.py           # Auditoría autenticada
│   └── infra_fingerprint.py # Fingerprinting de infraestructura
│
├── reports/
│   └── report_generator.py  # Reporte firmado RSA-SHA256
│
├── core/
│   └── db_security.py       # SQLite + Fernet (cifrado en reposo)
│
└── integrations/
    └── ollama_client.py     # Análisis IA local
```

Documentación de arquitectura completa: [docs/architecture.md](docs/architecture.md)

---

## Seguridad del toolkit

- API keys en `.env` — nunca commiteadas
- Pre-commit hook bloquea credenciales, PEM, PII antes de cada commit
- Base de datos cifrada — clave en archivo separado excluido del repo
- Todo escaneo activo requiere documento de autorización firmado digitalmente
- WireGuard con `AllowedIPs = 0.0.0.0/0` — kill-switch nativo a nivel kernel

---

## Metodología

Cada auditoría sigue este flujo obligatorio:

1. **Autorización** — documento firmado RSA-SHA256 antes de iniciar cualquier escaneo
2. **Reconocimiento** — OSINT pasivo, no intrusivo
3. **Análisis** — escaneo activo + análisis estático de código
4. **Entregable** — reporte firmado con hallazgos clasificados por severidad

**Regla fundamental:** ningún escaneo activo se inicia sin autorización firmada.

---

## Ética y legalidad

Este toolkit está diseñado exclusivamente para:
- Auditorías con **autorización escrita** del propietario del sistema
- Entornos de laboratorio controlados
- Plataformas de práctica autorizadas (Vulnweb, HackTheBox, OWASP)

Queda prohibido su uso contra sistemas sin autorización expresa.

---

## Contacto

Este repositorio es una **demo pública** del toolkit. El código fuente es privado.

Para auditorías o consultas profesionales:

📧 [infra.socrecon@proton.me](mailto:infra.socrecon@proton.me)  
🌐 [auditweb-landing](https://DaniAngel79.github.io/auditweb-landing/)

---

*Uso privado. Todos los derechos reservados.*
