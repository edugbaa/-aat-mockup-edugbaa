# CORAL

Landing que vende videos cinematográficos hechos con IA para anuncios de Airbnb.

- App: https://aat-mockup-edugbaa.vercel.app
- Stack: un solo `index.html` (HTML, CSS y JavaScript sin framework) desplegado en Vercel, con Supabase para autenticación y perfiles.

## Funciones

1. **Registro e inicio de sesión** (Supabase Auth, correo y contraseña). Al registrarse se guarda el nombre en `public.perfiles` (`id`, `nombre`, `creado_en`), con RLS para que cada usuario solo vea y edite su propio perfil.
2. **Formulario de nivel**: con nombre de la propiedad, link, tarifa por noche, número de fotos y correo, asigna el paquete:
   - Menos de 5 fotos: "Faltan fotos"
   - Tarifa menor a $500: Select ($450)
   - De $500 a $1,000: Premium ($650)
   - Más de $1,000 hasta $2,000: Luxury ($900)
   - Más de $2,000: Signature (cotización)

## Decisiones de diseño vs. sugerencias del IS

**1. Escalabilidad (diagnóstico de la Sesión 22)**
- **Qué propuso el IS:** el diagnóstico concluyó que ningún límite técnico se rompe ni a 1,000 veces mi tráfico (1.08 GB de los 100 GB de Vercel Hobby), y que mi cuello de botella es que Hobby solo permite uso personal o no comercial (Terms, sección 4).
- **Qué decidí:** Mantener Hobby mientras CORAL sea un prototipo y pasar a Vercel Pro ($20 al mes) antes de cobrarle al primer cliente.
- **Por qué:** abrí la fuente oficial y confirmé el límite de 100 GB. El tráfico no es el problema; el permiso de uso sí.

**2. Seguridad: llaves y RLS**
- **Qué propuso el IS:** en el plan de construcción del registro, usar solo la publishable key de Supabase en el frontend, no pedir la secret key y apoyar la protección de datos en RLS con políticas sobre `perfiles`.
- **Qué decidí:** Aceptar.
- **Por qué:** la publishable key es pública por diseño; cualquiera puede verla en el navegador. Si la secret key estuviera en el HTML, cualquier visitante podría saltarse RLS y leer o borrar todos los perfiles. Verifiqué en Supabase que `perfiles` muestra "RLS enabled" y 3 políticas.

**3. Seguridad: dependencia externa sin versión fija**
- **Qué propuso el IS:** el borrador del registro cargaba supabase-js desde un CDN con `@2`, que siempre descarga la última versión 2.x.
- **Qué decidí:** Revisar: fijé la versión a `2.45.4`.
- **Por qué:** con `@2`, una actualización o un paquete comprometido cambiaría el código que corre en mi página sin que yo lo sepa. Con versión fija, mi app siempre carga el mismo código que probé.

**4. Funcionalidad: error de visibilidad en el registro**
- **Qué propuso el IS:** el borrador ocultaba los formularios al iniciar sesión con el atributo `hidden`.
- **Qué decidí:** Rechazar esa parte y corregirla.
- **Por qué:** al probar el archivo con una simulación de Supabase, los formularios seguían visibles junto al saludo, porque la regla CSS `display: grid` le gana al atributo `hidden`. Agregué `#auth-forms[hidden] { display: none }` y repetí la prueba: tras iniciar sesión solo se ve el saludo.

**5. Funcionalidad: nivel Signature**
- **Qué propuso el IS:** al construir el formulario, agregó un cuarto nivel, "Signature", para tarifas mayores a $2,000. Mi especificación llegaba hasta $2,000 en Luxury.
- **Qué decidí:** Aceptar.
- **Por qué:** mi propia página ya habla de paquetes Signature desde $1,250 para villas de $2,000 o más, así que la función quedó coherente con el resto.

**6. Mockup y despliegue: botón de correo**
- **Qué propuso el IS:** en la primera versión del formulario, le puso un estilo extra al botón de correo, que le quitaba su efecto al pasar el cursor.
- **Qué decidí:** Rechazar.
- **Por qué:** yo había pedido "no cambies nada más de la página". Se lo pedí quitar y la versión final conserva el botón como estaba.

## Nota sobre el historial de commits

Mis primeros commits ("Add files via upload", "Create README.md") tienen mensajes genéricos porque subí los archivos desde la web de GitHub y acepté el mensaje por defecto. Esos commits contienen decisiones: "Add files via upload" fue la subida del formulario de nivel (decisiones 5 y 6), y desde entonces escribo mensajes que describen la decisión.
