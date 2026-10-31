# nuvoor-web: la landing de nuvoor.mx

Página de ventas de Nuvoor. Es estática: `index.html` + `img/`. No necesita servidor ni build para publicarse.

- `index.html` sale de `build.py` (en `../landing-fuentes.tar.gz`). Los cambios chicos de texto se pueden hacer directo aquí. Si se reconstruye, `build.py` lo vuelve a escribir.
- `img/*.webp` son capturas reales de los POS con datos de ejemplo. `img/marca/` trae el logo y `img/compartir.png` la imagen para compartir (1200×630).
- **Todos los botones** van a `https://app.nuvoor.mx/acceso.html` (`?alta=1&producto=taller|restaurant|tiendita`).

## Publicarla en nuvoor.mx

### 1. El repositorio (GitHub)

GitHub Pages da **un solo dominio por repo**, y `cotizador-heymakers` ya usa `app.nuvoor.mx`. Por eso la landing va en un repo aparte.

Con `gh`, desde esta carpeta:

```
git init -b main
git add -A
git commit -m "Landing de Nuvoor"
gh repo create nuvoor-web --public --source . --push
gh api -X POST repos/carlosmora0593-ship-it/nuvoor-web/pages -f "source[branch]=main" -f "source[path]=/"
gh api -X PUT  repos/carlosmora0593-ship-it/nuvoor-web/pages -f cname=nuvoor.mx
```

O a mano en github.com:

1. **New repository** → `nuvoor-web` → Public → Create.
2. **uploading an existing file** → arrastra `index.html`, `README.md` y la carpeta `img` completa → Commit.
3. **Settings → Pages** → Source: *Deploy from a branch* → `main` / `(root)` → Save.
4. En **Custom domain** escribe `nuvoor.mx` → Save. Con publicación desde rama, GitHub crea solo el archivo `CNAME` en el repo.

### 2. El DNS (GoDaddy → nuvoor.mx → DNS)

Usa **«Añadir nuevo registro»**, no el atajo de «Verifica la propiedad del dominio»: ese siempre escribe en `@` y no deja poner el nombre.

| Acción | Tipo | Nombre | Valor |
|---|---|---|---|
| Borrar | A | @ | WebsiteBuilder Site (76.223.105.230 / 13.248.243.5) |
| Agregar | A | @ | 185.199.108.153 |
| Agregar | A | @ | 185.199.109.153 |
| Agregar | A | @ | 185.199.110.153 |
| Agregar | A | @ | 185.199.111.153 |
| Cambiar | CNAME | www | carlosmora0593-ship-it.github.io |

**No tocar:**

- `CNAME app` (es la app).
- `TXT _github-pages-challenge-carlosmora0593-ship-it` (es la verificación).
- `MX`, `TXT v=spf1…`, `_dmarc`, `email` y `_domainkey`, que son del correo.

Opcional, IPv6: AAAA `@` → `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`.

Si GoDaddy no deja borrar el A del Website Builder, primero hay que desconectar el sitio de **Sitio web** en GoDaddy.

### 3. Encender y comprobar

1. Espera a que **Settings → Pages** diga «DNS check successful» y palomea **Enforce HTTPS**. Puede tardar hasta 24 h; casi siempre son minutos.
2. Abre `https://nuvoor.mx` y `https://www.nuvoor.mx`. GitHub redirige `www` a la raíz solo.
3. Para revisar el DNS sin `dig`: `https://dns.google/resolve?name=nuvoor.mx&type=A`.

### 4. Después

- En el repo `cotizador-heymakers`: `MakersOS.html` y `productos/*` deben redirigir a `https://nuvoor.mx`, igual que el «Volver a Nuvoor» de `acceso.html`.
- Si cambian los precios, cámbialos aquí **y** en la app.
