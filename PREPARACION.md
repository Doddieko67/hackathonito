### 1. Fidel (Arquitecto / Algoritmo / IA)
Fidel es el responsable de la arquitectura, el algoritmo de equidad (`lib/equity.ts`), la integración con Gemini y el despliegue en AWS[cite: 4]. También maneja las API Routes (BFF)[cite: 2].

*   **Tecnologías a aprender:** 
    *   Next.js 15 API Routes para el patrón BFF[cite: 1, 2].
    *   Gemini API (Flash) para la personalización cultural y la generación de itinerarios[cite: 1, 2].
    *   Despliegue en AWS (evaluando Amplify, EC2+Nginx o App Runner)[cite: 2, 4].
*   **Mini-proyecto preparatorio:** Crear un repositorio en Next.js con una ruta de API (`/api/recomendar`) que reciba un perfil cultural falso (mock). La API debe conectarse a Gemini Flash para devolver un JSON estructurado con un itinerario básico. Finalmente, desplegar esta pequeña API funcional en AWS usando el nivel gratuito.

### 2. Emi (Frontend Turista)
Emi tiene a su cargo el flujo del turista, la integración de mapas, la interfaz de tarjetas y el funcionamiento sin conexión[cite: 4].

*   **Tecnologías a aprender:** 
    *   Next.js 15 y Tailwind CSS para las vistas del turista[cite: 1, 4].
    *   Mapbox GL JS para renderizar los pines de los negocios[cite: 1, 4].
    *   PWA Cache API y `localStorage` para guardar el itinerario y permitir el acceso offline[cite: 1, 3].
*   **Mini-proyecto preparatorio:** Construir una vista móvil (Mobile-First) en Tailwind que muestre un mapa de Mapbox en la mitad superior y una pila de "tarjetas deslizables" en la mitad inferior. Implementar lógica en JavaScript para que la información de esas tarjetas se guarde en `localStorage` y siga siendo visible al recargar la página sin conexión a internet.

* Tecnologia recomendada: Stitch de Google

### 3. Farid (Frontend Admin)
Farid se encarga del panel de administración, la autenticación y la subida de fotografías[cite: 4].

*   **Tecnologías a aprender:** 
    *   Next.js 15 y Tailwind CSS para el dashboard administrativo[cite: 1, 4].
    *   Supabase Auth para el inicio de sesión de los administradores/negocios[cite: 3].
    *   Cloudinary para la carga y optimización automática de imágenes[cite: 1, 4].
*   **Mini-proyecto preparatorio:** Crear una pantalla de inicio de sesión (`/login`) protegida con Supabase Auth. Una vez autenticado, redirigir a un dashboard protegido donde el usuario pueda seleccionar una foto de su computadora, subirla exitosamente a Cloudinary mediante su API, y mostrar la imagen renderizada en la pantalla.

* Tecnologia recomendada: Stitch de Google

### 4. Alan (Backend / Base de Datos)
Alan es el dueño de la infraestructura de datos, el esquema SQL, los endpoints principales y el manejo en tiempo real[cite: 4].

*   **Tecnologías a aprender:** 
    *   Supabase (PostgreSQL) para la base de datos[cite: 4].
    *   Supabase Realtime para detectar la saturación de los negocios al momento[cite: 1, 3].
    *   TypeScript estricto para definir los contratos de las API (`/api/negocios`, etc.)[cite: 2, 4].
*   **Mini-proyecto preparatorio:** Configurar un proyecto en Supabase y escribir un esquema SQL básico con dos tablas relacionadas (ej. `negocios` y `visitas`). Construir una API Route en Next.js que realice operaciones CRUD (Crear, Leer, Actualizar, Borrar) sobre la tabla `negocios` e implementar una suscripción de Supabase Realtime que imprima en consola cada vez que se agregue una nueva "visita".

### 5. Xavier (Apoyo Backend / QA)
Xavier es el responsable de los datos de prueba, la documentación y el testing del proyecto[cite: 4].

*   **Tecnologías a aprender:** 
    *   Generación de datos estructurados (Mocking)[cite: 4].
    *   Fundamentos de bases de datos relacionales para entender el esquema de Supabase[cite: 4].
    *   Herramientas de pruebas para API (como Postman, Insomnia o Jest/Playwright).
*   **Mini-proyecto preparatorio:** Escribir un script en TypeScript o Node.js que genere un archivo JSON con 50 micronegocios falsos (con coordenadas, nombres, y estado de verificación "Ola México"). Luego, configurar una colección en Postman para probar que los endpoints que Alan desarrolle respondan correctamente con estos datos.

---

¿Hay alguna restricción específica, como los límites de la capa gratuita en los servicios de AWS, Supabase o Mapbox, que el equipo deba considerar antes de comenzar estos mini-proyectos?
