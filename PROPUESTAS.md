# MexGo

**Genius Arena Hackathon 2026**
**Track Fundación Coppel & Coppel Emprende**

**MexGo**
Plataforma de visibilidad turística con IA para micronegocios locales del sector hospitalidad

> "Queremos que el turista explore el México auténtico, y que ese México le llegue directamente al bolsillo a quienes más lo necesitan."

**Equipo:** Los Mossitos
**Documento:** Fase 1 Ideación
**Track:** Cancha justa en el mundial para los negocios turísticos locales
**Convocatoria:** Copa Mundial de Fútbol FIFA 2026

---

## Alto impacto: el problema y a quién afecta

### Problema central
La Copa Mundial de Fútbol FIFA 2026 representa una oportunidad económica histórica para México: 600 millones de dólares de derrama esperada en la Ciudad de México, más de un millón de turistas extranjeros y 300 mil nuevos empleos en hospitalidad y servicios. Sin embargo, esta oportunidad no llega de forma equitativa, y ese es el problema que resolvemos.

### El ciclo de invisibilidad que excluye a los micronegocios
Las plataformas de turismo existentes Google Maps, TripAdvisor, Airbnb Experiences favorecen sistemáticamente a los negocios con mayor presupuesto de marketing y mayor número de reseñas acumuladas. Esto genera un ciclo que se refuerza solo: los turistas van a los mismos lugares, esos lugares acumulan más reseñas, y los micronegocios locales permanecen invisibles aunque ofrezcan una experiencia auténtica y de calidad.

El resultado es que las grandes cadenas acaparan el beneficio económico del Mundial mientras que los micro y pequeños negocios quedan excluidos de la derrama, profundizando desigualdades económicas existentes. Los partidos se jugarán en tres zonas metropolitanas: Ciudad de México (5 partidos, incluido el inaugural), Nuevo León (4 partidos) y Jalisco (4 partidos), concentrando el flujo turístico exactamente donde estos negocios operan y donde la desigualdad de visibilidad es más crítica.

### El problema del turista extranjero
El turista llega a una ciudad desconocida, con un idioma diferente y sin redes de confianza locales. Ante la incertidumbre, su respuesta natural es buscar lo conocido: marcas internacionales, cadenas, lugares con miles de reseñas en su idioma. No por preferencia, sino porque ninguna plataforma le habla desde su contexto cultural ni le da razones concretas para confiar en algo nuevo.

**Nadie le está hablando en su idioma, adaptado a su cultura, con lugares que pueda visitar con confianza real. Esa brecha es la que MexGo resuelve.**

### El ecosistema que ya existe: Ola México
El programa Ola México, implementado por Impact Hub CDMX con apoyo de Fundación Coppel, ya capacitó y profesionalizó micronegocios locales de hospitalidad en las tres zonas metropolitanas del Mundial. Estos negocios cuentan con formación en atención al cliente, marketing digital, finanzas y sostenibilidad, y tienen un sello de confianza validado. **Lo que les falta es el canal que los conecte con el turista en el momento exacto en que éste los necesita. MexGo es ese canal.**

---

## Innovación y creatividad: la solución propuesta

### La solución
MexGo es una Progressive Web App (PWA) conversacional con inteligencia artificial que conecta turistas con micronegocios locales verificados por Ola México, usando un algoritmo de recomendación diseñado explícitamente para distribuir el tráfico de forma equitativa, no para concentrarlo. Es la única herramienta de turismo cuyo objetivo de diseño es que el turista no vaya siempre al mismo lugar.

### Tres capas integradas que ninguna app combina hoy
1. **Micronegocios locales verificados por Ola México** siempre presentes, siempre priorizados. Son el núcleo de cada recomendación.
2. **Monumentos y puntos de interés histórico** presentes como complemento para enriquecer la experiencia y llevar al turista a zonas fuera de los circuitos turísticos habituales, cerca de los micronegocios.
3. **Contexto del partido** presente solo si el turista tiene boleto. Si no tiene o prefiere explorar, el partido desaparece de la interfaz y la IA construye el itinerario alrededor de los otros dos ejes.

### Flujo de experiencia del turista
El turista abre la app sin descargar nada (accede vía código QR o enlace directo) y ve una pantalla de bienvenida con dos opciones: contestar un cuestionario breve para recibir recomendaciones personalizadas, o explorar de inmediato con sugerencias generadas a partir del idioma de su dispositivo.

*   **Sin cuestionario:** la IA infiere el perfil cultural a partir del idioma detectado y genera recomendaciones sintéticas adaptadas a ese país. Sin fricción desde el primer segundo.
*   **Con cuestionario:** la IA hace preguntas en formato conversacional, no como formulario:
    *   ¿De dónde vienes?
    *   ¿Vienes solo o acompañado, y hay niños en el grupo?
    *   ¿Cuánto tiempo te quedas en México?
    *   ¿Qué prefieres durante toda tu estancia: gastronomía, cultura, deporte, o combinar?

Con estas respuestas, la app presenta un calendario interactivo de toda la estancia: el turista toca los bloques horarios que quiere libres, o los que quiere que la IA llene con sugerencias de actividades. La IA arma un itinerario de varios días acoplado al tiempo real disponible, y puede hacer preguntas adicionales si lo considera necesario para afinar el plan. Si el turista prefiere espontaneidad, puede dejar bloques abiertos.

Después del cuestionario, la app pregunta si el turista tiene boleto para algún partido. Si sí, ese partido ancla el itinerario de ese día. Si no, todas las referencias al partido desaparecen de la interfaz.

Todo es editable en cualquier momento desde la barra de navegación inferior: cuestionario, boleto, preferencias. El turista que se equivocó de país o que cambió de planes no pierde su sesión, solo actualiza su perfil.

El itinerario generado se guarda localmente en el dispositivo sin necesidad de cuenta ni inicio de sesión, y funciona en modo sin conexión para consulta, útil en el estadio o en el metro sin datos móviles.

### El motor de recomendación con equidad: el diferenciador central
El algoritmo calcula un score de visibilidad en tiempo real para cada negocio:

`Score = (relevancia × proximidad) + Bola México - saturación`

*   `$\beta_{OlaMexico}$`: bonus fijo para negocios verificados por el programa.
*   `saturación`: penalización creciente según cuántos clientes ya recibió ese negocio hoy vía la app. Un negocio saturado baja en el ranking para dar espacio a otros.
*   **Desempate:** gana el negocio que lleva más tiempo sin recibir un cliente referido.

El resultado siempre muestra entre 4 y 6 opciones, nunca una sola. El turista nunca ve el mecanismo; solo percibe que las recomendaciones tienen sentido para él.

### Personalización cultural por país de origen
Antes de generar recomendaciones, la IA investiga tres parámetros del país de origen: experiencias culturalmente familiares, rango de precio percibido como accesible en pesos mexicanos, y disposición de caminata típica. Estos parámetros ajustan los filtros de relevancia antes de que el algoritmo de equidad entre en juego. La personalización cultural alimenta al algoritmo; no lo reemplaza.

### Presentación de resultados: diseño deliberado
La pantalla principal muestra un mapa pequeño arriba con los pins de los negocios recomendados, y una pila de tarjetas deslizables abajo. Cada tarjeta muestra: foto del lugar con sello Ola México si aplica, nombre y tipo en dos palabras, y tres señales de confianza: minutos caminando, turistas que lo visitaron esta semana, y horario de hoy.

Lo que deliberadamente no existe: calificaciones de estrellas (favorecen a los ya famosos), reseñas de texto (los turistas no las leen en el momento), precios exactos (varían y generan expectativas), anuncios, "destacado" o "patrocinado", y más de seis tarjetas por sesión. La confianza se comunica con datos sociales concretos, nunca con la frase "zona segura".

---

## Viabilidad: tecnologías y tiempos

### Stack tecnológico
El stack fue seleccionado para maximizar velocidad de desarrollo en el contexto del hackathon, minimizar costos de infraestructura, y garantizar que el prototipo sea funcional y demostrable ante el jurado.

| Componente | Tecnología | Justificación |
| :--- | :--- | :--- |
| **Frontend / PWA** | Next.js 15 | PWA sin descarga, QR directo, multilenguaje |
| **Base de datos** | Supabase | Tiempo real, auth de negocios, escalable |
| **IA** | Gemini API (Flash) | Personalización cultural, conversacional itinerario, chat |
| **Mapas** | Mapbox GL JS o Google Maps (aun se discute esto) | Personalizable, estilos propios, bajo costo |
| **Traducciones** | i18next + DeepL API | Multilenguaje fluido para turistas |
| **Imágenes** | Cloudinary | Optimización automática, carga rápida |
| **Offline** | PWA Cache API | Itinerario disponible sin conexión |

La arquitectura es coherente con el tamaño del problema: no requiere infraestructura propia, usa servicios gestionados con niveles gratuitos suficientes para el demo, y puede escalar a producción sin cambiar el stack. El prototipo funcional para la Fase 2 es alcanzable dentro de los tiempos del hackathon dado que los módulos están claramente delimitados y pueden desarrollarse en paralelo por el equipo.

---

## Diferenciación frente a la competencia

### Diferenciación
Ninguna plataforma de turismo existente fue diseñada con el objetivo explícito de distribuir el tráfico de forma equitativa. Todas fueron diseñadas para concentrarlo en los mejor evaluados. MexGo es la primera.

| Característica | Google Maps | TripAdvisor | Airbnb Exp. | MexGo |
| :--- | :--- | :--- | :--- | :--- |
| **Algoritmo** | Popularidad | Reseñas | Precio alto | Equidad activa |
| **Personalización** | Búsqueda | Historial | Ninguna | Perfil cultural |
| **Micronegocios** | Sin prioridad | Sin prioridad | Barrera de entrada | Prioridad central |
| **Sello Ola México** | No | No | No | Integrado |
| **Confianza** | Estrellas | Reseñas texto | Fotos | Señales sociales |
| **Instalación / Offline** | App nativa / No | App nativa / No | App nativa / No | Ninguna (QR/PWA) / Sí |

La diferenciación más importante no es tecnológica: es de intención de diseño. Un turista que descubre una taquería familiar auténtica que ningún otro turista conocía ayer tiene una mejor experiencia que el que hizo fila en el restaurante de siempre. Esa es la propuesta de valor para el turista. Y ese turista es el canal de distribución económica para el micronegocio.

---

## Escalabilidad: más allá del Mundial
MexGo no termina con el partido final. El directorio de Ola México, el algoritmo de equidad y la infraestructura de personalización cultural son completamente reutilizables para cualquier evento de alta demanda turística:

*   **Eventos recurrentes:** Gran Premio de Fórmula 1 en México, Festival Internacional Cervantino, Guadalajara International Film Festival.
*   **Turismo nacional:** el mismo sistema funciona para turistas mexicanos que visitan otras ciudades en fines de semana o periodos vacacionales.
*   **Expansión del sello:** el sello Ola México puede evolucionar hacia una certificación permanente de negocios locales verificados, con renovación anual.
*   **Integración futura:** con Coppel Emprende para acceso a crédito o formalización, con sitios gubernamentales de turismo, y con sistemas de pago locales como CoDi o Mercado Pago para transacción directa desde la app.
*   **Modelo replicable:** la arquitectura puede adaptarse a otros países con contextos similares de alta demanda turística y desigualdad de visibilidad entre grandes cadenas y micronegocios locales.

El impacto no es solo durante el Mundial. Es la capacidad instalada que queda en los negocios: visibilidad digital probada, datos de comportamiento turístico, y un canal de conexión directa con clientes internacionales que pueden volver o recomendar.

---

## Mérito técnico: cómo se aprovechan las herramientas
El valor técnico de MexGo no está en usar muchas herramientas, sino en cómo se integran para resolver el problema:

*   **Gemini API** no solo traduce ni responde preguntas: investiga el contexto económico y cultural del país de origen del turista antes de generar cada recomendación, y adapta el itinerario de varios días en función de los bloques horarios marcados en el calendario interactivo. Es el cerebro que convierte datos en experiencia personalizada.
*   **El algoritmo de equidad** es lógica propia, no una API externa. Es el componente más innovador del sistema y el que directamente resuelve el problema de concentración económica que ninguna plataforma existente aborda.
*   **La PWA con caché offline** resuelve un problema real del contexto: el turista en el estadio, en el metro o en una zona con señal saturada durante el Mundial necesita acceder a su itinerario sin depender de internet. Es un detalle de diseño que refleja conocimiento del usuario real, no solo del usuario ideal.
*   **Supabase en tiempo real** permite que el algoritmo de saturación funcione: si muchos turistas están siendo referidos al mismo negocio en este momento, el sistema lo detecta y ajusta el ranking de inmediato, no en el siguiente ciclo de actualización.
