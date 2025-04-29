<script>
  //Posibles colores de las formas que incrementan el porcentaje
  const colores = ["#F9D01E", "#000000", "#004592", "#E72F24"];

  //Posibles formas a usar y el valor porcentual que aportan al ser coloreadas
  const formas = [
    { width: 23.5, height: 24.5, percentage: 1 },
    { width: 10.7, height: 10.7, percentage: 1 },
    { width: 2.7, height: 24.5, percentage: 1 },
    { width: 16.2, height: 24.5, percentage: 1 },
  ];

  //Valores de porcentaje a representar
  let numeros = [0, 24, 33, 42, 54, 63, 71, 77, 87, 92, 98, 100];

  //Funcion que devuelve un array de las formas necesarias para representar una magnitud pasada por parametro
  function calcularFormas(magnitud) {
    let porcentaje = 0;
    let lista = [];

    while (porcentaje < magnitud) {
      //Seleccionamos una forma aleatoria de las posibles. El math.random() genera un numero aleatorio de 0 a 1 y el math.floor() lo
      //redondea hacia abajo, de esta manera al multiplicarlo por el tamaño del array de formas conseguimos una forma aleatoria
      const f = formas[Math.floor(Math.random() * formas.length)];

      //Si el numero generado es mayor a 0.5, se rota la forma y si no se mantiene sus dimensiones originales
      const rotada =
        Math.random() > 0.5
          ? { width: f.height, height: f.width, percentage: f.percentage }
          : f;

      //Verificamos si se puede agregar o no la forma sin que supere el valor a representar
      if (porcentaje + rotada.percentage <= magnitud) {
        lista.push(rotada);
        porcentaje += rotada.percentage;
      }
    }

    return lista;
  }

  // Función que busca una posición aleatoria para una forma sin que se superponga con otras ya ubicadas
  function encontrarPos(forma, ubicadas) {
    let intentos = 0;

    //Permitimos hasta 100 intentos para encontrar un lugar sin superposicion para que no se generen ciclos infinitos
    while (intentos < 100) {
      const x = Math.random() * (300 - forma.width); //Posicion x aleatoria
      const y = Math.random() * (300 - forma.height); //Posicion y aleatoria

      let overlap = false;
      for (let i = 0; i < ubicadas.length; i++) {
        const f = ubicadas[i];
        if (
          //Nos fijamos si se superpone con alguna otra forma
          x < f.x + f.width &&
          x + forma.width > f.x &&
          y < f.y + f.height &&
          y + forma.height > f.y
        ) {
          overlap = true;
          break;
        }
      }
      if (!overlap) return { x, y }; //Si no se superponen devuelvo las posiciones
      intentos++;
    }
    return null;
  }

  // Función que coloca las formas dentro del SVG de manera aleatoria, evitando superposiciones
  function generarFormas(magnitud) {
    const formas_color = calcularFormas(magnitud); // Veo la cantidad de formas necesarias para representar la magnitud
    const formas_restantes = 100 - formas_color.length; // Veo las formas blancas necesarias para completar el 100%
    const ubicadas = [];
    const svg = [];

    // Colocar las formas con color
    formas_color.forEach((f) => {
      const pos = encontrarPos(f, ubicadas); // Le paso ubicadas como argumento
      if (pos) {
        const color = colores[Math.floor(Math.random() * colores.length)]; //Elegimos un color aleatorio
        const id = Math.floor(Math.random() * 1000000); // Genera un ID entre 0 y 999999, de esta manera los IDs de cada forma van a tener menos probabilidad de ser iguales
        const rect = {
          //Pasamos los parametros de la figura
          x: pos.x,
          y: pos.y,
          width: f.width,
          height: f.height,
          color,
          id,
        };
        ubicadas.push(rect); //Guardamos la figura en el array que se usa para recordar dónde ya pusimos formas
        svg.push(rect); //Este array representa todas las formas que se van a ver en el grafico final
      }
    });

    // Colocar las formas blancas (vacias)
    for (let i = 0; i < formas_restantes; i++) {
      const f = formas[Math.floor(Math.random() * formas.length)];
      const rotada =
        Math.random() > 0.5 ? { width: f.height, height: f.width } : f; //Chequea si rotarla o no
      const pos = encontrarPos(rotada, ubicadas); // Le paso ubicadas como argumento
      if (pos) {
        //Misma idea que con las formas coloreadas
        const id = Math.floor(Math.random() * 1000000); // genera un ID simple con números aleatorios
        const rect = {
          x: pos.x,
          y: pos.y,
          width: rotada.width,
          height: rotada.height,
          color: "#fff",
          id: id,
        };
        ubicadas.push(rect);
        svg.push(rect);
      }
    }

    return svg;
  }
</script>

<main class="container">
    <!-- Estructura contenido HTML -->
  <body>
    <h1>ChatGPT y el arte: Estiman que en 2033 controlará la industria creativa</h1>
    <h3>
      El lanzamiento de ChatGPT inició una revolución en el arte. 
      La creación humana pierde terreno frente al avance imparable de la inteligencia artificial.
    </h3>

    <p>
      Desde su aparición en 2022, ChatGPT transformó la industria artística. 
      <br>
      Lo que empezó como una herramienta de apoyo rápidamente evolucionó en un actor principal 
      capaz de generar obras, conceptos y estilos propios.
      <br /><br />
      En 2023, surgieron inteligencias artificiales especializadas en imagen, música y literatura, 
      acelerando la automatización de la producción cultural. 
      <br>
      Hoy, las proyecciones indican que para 2033, 
      el 100% de la industria del arte estará en manos de ChatGPT, 
      desplazando a la creación humana.
    </p>

  <h5><strong><u>La expansión de ChatGPT en el arte:</u></strong></h5>
    <div id="graficos">
      {#each numeros as porcentaje, i}
        <!-- Itera sobre cada número del array de numeros, accediendo al valor como porcentaje y al índice como i-->
        <div class="anio-bloque">
          <div class="anio">{2021 + i}</div>
          <!-- Calcula y muestra el año correspondiente, empezando en 2000 y sumando de a 5 años según el índice -->

          {#if porcentaje < 0 || porcentaje > 100}
            <!-- Si el porcentaje no es válido (menor a 0 o mayor a 100) -->
            <div class="grafico no-representable">No es representable</div>
          {:else}
            <svg class="grafico" viewBox="0 0 300 300">
              <!-- Se crea un SVG con vista de 300x300 píxeles -->
              {#each generarFormas(porcentaje) as f (f.id)}
                <!-- Se generan las formas según el porcentaje, cada una con un id único -->
                <rect
                  x={f.x}
                  y={f.y}
                  width={f.width}
                  height={f.height}
                  fill={f.color}
                  stroke="black"
                  stroke-width="0.5"
                />
                <!-- Dibuja un rectángulo con las propiedades de cada forma -->
              {/each}
            </svg>
          {/if}

          <div class="porcentaje">{porcentaje}%</div>
          <!-- Muestra el valor del porcentaje numéricamente debajo del gráfico -->
        </div>
      {/each}
    </div>
 
    <p> 
      Los gráficos muestran cómo ChatGPT, desde su lanzamiento en 2022, transformó rápidamente la industria artística. 
      En 2021, el arte era completamente humano, pero en apenas un año la inteligencia artificial ya había generado el 
      24 % de las obras. 
      <br /><br /> 
      Cada recuadro en los gráficos representa un año entre 2021 y 2032. 
      <br>
      Dentro de cada uno, los colores y formas simbolizan 
      las obras de arte: los recuadros coloreados (rojo, azul, amarillo y negro) representan las piezas generadas por ChatGPT, 
      mientras que los recuadros en blanco simbolizan las obras humanas. El porcentaje indicado debajo de cada cuadro muestra
      la proporción de arte generado por ChatGPT en ese año.
      <br><br>
      La llegada de inteligencias especializadas en 2023 aceleró aún más este proceso: el porcentaje de arte creado por 
      ChatGPT superó el 50 % en 2025, <br> y las estimaciones actuales indican que para 2032 la producción humana quedará 
      completamente desplazada. 
      <br /><br /> 
      Este avance despierta tanto entusiasmo como preocupación. Muchos celebran la eficiencia y la innovación que 
      aportan las nuevas tecnologías, pero otros advierten que la creación artística podría perder su esencia humana. 
      <br /><br /> 
    </p>

    <h5><strong><u>Crecimiento de ChatGPT y NightCafe en el arte:<u></strong></h5>

    <div 
      class="flourish-embed flourish-chart" 
      data-src="visualisation/22896036">
      <script 
      src="https://public.flourish.studio/resources/embed.js">
      </script>
      <noscript>
        <img 
        src="https://public.flourish.studio/visualisation/22896036/thumbnail" 
        width="100%" 
        alt="chart visualization" />
      </noscript>
    </div> 
   
    <br />
    <br />

    <p>
      El gráfico muestra la evolución de la participacion de la inteligencia artificial en el arte
      entre 2021 y 2025, principalmente comparando a ChatGPT y NightCafe. ChatGPT, lanzado en 2022, creció 
      rápidamente gracias a su versatilidad y adopción masiva en diversas áreas creativas. 
      <br /><br /> 
      NightCafe, surgido en 2023, se enfocó exclusivamente en la generación de imágenes artísticas.
      Aunque tuvo un crecimiento veloz, ChatGPT se mantuvo por encima en volumen de mercado. 
      <br /><br /> 
      La diferencia refleja dos estrategias: integración amplia frente a especialización en arte visual,
      mostrando cómo la inteligencia artificial transforma cada vez más rápido la industria cultural. 
    </p>
    
    <h5><strong><u>Proyección de participación de ChatGPT y NightCafe:</u></strong></h5>
    <div 
      class="flourish-embed flourish-chart2" 
      data-src="visualisation/22896129">
      <script 
      src="https://public.flourish.studio/resources/embed.js">
      </script>
      <noscript>
        <img 
        src="https://public.flourish.studio/visualisation/22896129/thumbnail" 
        width="100%" 
        alt="chart visualization" />
      </noscript>
    </div>

    <p>
      El gráfico muestra las estimaciones de participación en la industria artística de ChatGPT y NightCafe entre 2026 y 2032. 
      A partir de 2026, ChatGPT empieza a dominar el mercado, ampliando su presencia año tras año frente a NightCafe. 
      <br /><br /> 
      Mientras NightCafe mantiene cierta resistencia en los primeros años, su participación disminuye progresivamente. 
      Para 2032, las proyecciones indican que ChatGPT generaría el 100 % del arte, 
      desplazando por completo a sus competidores. <br /><br /> Estos datos reflejan la creciente centralización del mercado 
      creativo en torno a plataformas de IA más integrales, cambiando radicalmente el futuro del arte y de la producción cultural. 
    </p>

    <br /><br /> 

    <footer class="footer">
      <div class="personas">
        <div class="persona">
          <h3>Rosa Maria Tagle Zorraquin</h3>
          <div class="social">
            <img src="/images/Instagram.png" alt="Instagram" />
            <a
              href="https://www.instagram.com/rochitagle4934?utm_source=ig_web_button_share_sheet&igsh=ZDNlZDc0MzIxNw=="
              target="_blank"
            >
              <span>@rochitagle4934</span>
            </a>
          </div>
          <div class="social">
            <img src="/images/LinkedIn.png" alt="LinkedIn" />
            <a
              href="https://www.linkedin.com/public-profile/settings?lipi=urn%3Ali%3Apage%3Ad_flagship3_profile_self_edit_contact-info%3BFLSycjfkS6isUZak7HmPtw%3D%3D"
              target="_blank"
            >
              <span>Rosa Tagle Zorraquín</span>
            </a>
          </div>
        </div>

        <div class="divisor"></div>

        <div class="persona">
          <h3>Victoria Stefania Schenone</h3>
          <div class="social">
            <img src="/images/Instagram.png" alt="Instagram" />
            <a
              href="https://www.instagram.com/stefanyred?utm_source=ig_web_button_share_sheet&igsh=ZDNlZDc0MzIxNw=="
              target="_blank"
            >
              <span>@stefanyred</span>
            </a>
          </div>
          <div class="social">
            <img src="/images/LinkedIn.png" alt="LinkedIn" />
            <a
              href="https://www.linkedin.com/in/victoria-stefania-schenone-fern%C3%A1ndez-1ab05428b?lipi=urn%3Ali%3Apage%3Ad_flagship3_profile_view_base_contact_details%3BxD51DGzHSByEYrcRj15UUg%3D%3D"
              target="_blank"
            >
              <span>Victoria Stefania Schenone Fernández</span>
            </a>
          </div>
        </div>
      </div>
    </footer>
  </body>
</main>

<!-- Estilos CSS -->
<style>
  /* Estilo Titulo Principal */
  h1 {
    text-align: center;
    color: #333;
  }

  /* Estilo para la cita */
  h3 {
    font-style: italic;
    text-align: left;
    color: #666;
  }

  h5 {
  text-align: left;
  display: block;
  width: 100%;
  margin-left: 0;
  color: #333;
  }

  /* Estilo para los párrafos */
  p {
    font-size: 20px;
    text-align: left;
    line-height: 1.6;
    color: #444;
  }

  #graficos {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 40px;
    padding: 20px;
    justify-items: center;
  }

  .anio-bloque {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 300px;
  }

  .anio {
    font-size: 20px;
    font-family: Georgia, serif;
    margin-bottom: 10px;
  }

  .grafico {
    width: 300px;
    height: 300px;
    border: 1.2px solid #000000;
  }

  .porcentaje {
    font-size: 20px;
    font-weight: bold;
    margin-top: 10px;
    font-family: Georgia, serif;
  }

  .footer {
    background-color: #152a4e;
    color: white;
    font-family: sans-serif;
    width: 300% !important;
    padding: 40px 80px;
  }

  .personas {
    display: flex;
    justify-content: center;
    align-items: flex-start;
    gap: 100px;
    flex-wrap: nowrap;
  }

  .persona {
    flex-direction: column;
    gap: 12px;
    width: 220px;
  }

  .persona h3 {
    font-size: 16px;
    color: white;
    font-style: georgia, sans-serif;
    font-weight: bold;
    margin-bottom: 30px;
  }

  .social {
    display: flex;
    margin-bottom: 10px;
    align-items: center;
    gap: 10px;
    margin-bottom: 20px;
  }

  .social a {
    color: white;
    text-decoration: none;
    font-size: 14px;
  }

  .social a:hover {
    text-decoration: underline;
    color: aqua;
  }

  .social img {
    width: 24px;
    height: 24px;
  }

  .divisor {
    width: 2px;
    background-color: white;
    height: auto;
    min-height: 100%;
    align-self: stretch;
  }

  .flourish-embed.flourish-chart2 {
    width: 100%;
    max-width: 100%;
    height: auto;
    margin: 10px 10px;
    position: relative;
  }

  .flourish-embed.flourish-chart {
    width: 100%;
    max-width: 100%;
    height: 90%;
    margin: 10px;
  }
  :global(.fl-layout-wrapper) {
    background-color: #eaeaea;
  }
</style>
