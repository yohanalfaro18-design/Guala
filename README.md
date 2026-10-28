# Guala Chatbot

El chat consulta las tablas públicas `menu_items` y `business_info`, y usa la función `search_menu` para encontrar platos relevantes. Groq responde consultas a través de una Supabase Edge Function; su clave se guarda como secreto y no se incluye en el HTML. La función acepta llamadas públicas para que el chat pueda funcionar sin iniciar sesión; monitoreá su uso y los límites de Groq.

El chat informa sobre la carta y el local. No guarda conversaciones, pedidos ni reservas: esos datos no forman parte del esquema actual.

Cuando una consulta de precio tiene una respuesta con precios y el número de WhatsApp está publicado en `business_info`, aparece un botón para continuar el pedido con un mensaje precompletado. El botón abre WhatsApp; el cliente revisa y envía el mensaje desde allí.

## Configuración

1. El HTML ya está configurado con la URL y la clave publicable de este proyecto Supabase. Si cambiás de proyecto, actualizá `SUPABASE_URL` y `SUPABASE_ANON_KEY`.
2. Desde la raíz del proyecto, iniciá sesión con Supabase CLI y vinculá el proyecto:

   ```sh
   supabase login
   supabase link --project-ref tvdxfezmqjzevbtdnpif
   ```

3. Guardá una clave de API activa de Groq como secreto (ejecutá el comando localmente y reemplazá el valor de ejemplo; no la pongas en el HTML):

   ```sh
   supabase secrets set GROQ_API_KEY=TU_CLAVE_DE_GROQ
   ```

   Opcionalmente, podés elegir otro modelo con el secreto `GROQ_MODEL`.
4. Publicá la función:

   ```sh
   supabase functions deploy guala-chat
   ```

La base debe tener aplicados el esquema y los datos iniciales que compartiste. La página consulta hasta 12 resultados de `search_menu` y los datos públicos del local para responder cada pregunta.

## Despliegue de la web en Vercel

La interfaz es un sitio estático. `vercel.json` dirige la ruta principal (`/`) a `guala-chatbot.html`; la carpeta `supabase` se excluye del despliegue web porque la función de Groq sigue ejecutándose en Supabase.

Desde la carpeta del proyecto, instalá e iniciá sesión en Vercel CLI si todavía no lo hiciste y desplegá a producción:

```sh
npm install --global vercel
vercel login
vercel --prod
```

En la primera ejecución, elegí crear un proyecto y usá la carpeta actual como directorio del proyecto. No hace falta configurar un comando de build ni variables de entorno de Vercel para este sitio. La clave de Groq permanece como secreto `GROQ_API_KEY` en Supabase; no la cargues en Vercel ni en el HTML.
