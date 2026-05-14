# Arquitectura — AuditWeb Toolkit

> Diagrama de alto nivel. El código fuente es privado.

## Flujo de auditoría

```
┌─────────────────────────────────────────────────────┐
│                    audit_web.py                      │
│                  (CLI principal)                     │
└──────────────┬──────────────────────────────────────┘
               │
       ┌───────┼────────────────────────────┐
       │       │                            │
       ▼       ▼                            ▼
┌──────────┐ ┌──────────────┐  ┌──────────────────────┐
│ Recon    │ │  Análisis    │  │  Report Generator    │
│ Pasivo   │ │  (Whitebox + │  │  RSA-SHA256 signing  │
│ (OSINT)  │ │  Dinámico)   │  │  PDF/TXT output      │
└──────────┘ └──────────────┘  └──────────────────────┘
       │             │                      │
       └─────────────┴──────────────────────┘
                     │
              ┌──────▼──────┐
              │  SQLite DB  │
              │  + Fernet   │
              │  (cifrado)  │
              └─────────────┘
```

## Módulos

### commercial_recon
Reconocimiento pasivo OSINT. No intrusivo. Fuentes:
- DNS (dnspython)
- WHOIS (python-whois)
- Wayback Machine CDX API
- VirusTotal API v3
- AbuseIPDB API v2
- Shodan API
- Detección WAF/tech (análisis de headers HTTP)

### modules/whitebox
Análisis estático de código fuente del cliente:
- **Semgrep** — patrones SAST configurables
- **Trufflehog** — secretos y credenciales hardcodeadas

### modules/graybox
Auditoría autenticada (caja gris):
- Análisis con credenciales de acceso provistas por el cliente
- Permite evaluar lógica de autorización y sesión

### reports/report_generator
Generación de reportes:
- Formato TXT estructurado
- Firma RSA-SHA256 (clave privada local)
- Documento de autorización firmado antes de cada auditoría
- Hash SHA256 del reporte para verificación de integridad

### core/db_security
Almacenamiento cifrado:
- SQLite como motor de base de datos
- Fernet (AES-128 CBC + HMAC-SHA256) para cifrado en reposo
- Clave de cifrado separada del repositorio

### integrations/ollama_client
IA local sin dependencia de nube:
- Análisis de hallazgos con modelos locales (Ollama)
- Modelo personalizado con instrucciones anti-alucinación
- Cero datos enviados a servicios externos

## Seguridad operacional

```
┌─────────────────────────────────────┐
│  Kali Linux (auditor)               │
│  ┌─────────────────────┐            │
│  │  WireGuard VPN      │            │
│  │  kill-switch kernel  │            │
│  └────────┬────────────┘            │
└───────────┼─────────────────────────┘
            │ túnel cifrado
            ▼
┌─────────────────────────────────────┐
│  VPS (salida de tráfico)            │
│  IP pública estática                │
└─────────────────────────────────────┘
```

- Todo el tráfico de auditoría sale por el VPS vía WireGuard
- Si VPN cae: kill-switch corta toda conectividad (nivel kernel)
- API keys solo en `.env` local — nunca en el repo
- Pre-commit hook bloquea credenciales antes de cada commit

---

*Código fuente privado. Contacto: infra.socrecon@proton.me*
