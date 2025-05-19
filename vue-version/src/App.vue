
<template>
  <div>
    <header>
      <h1>Directorio de Personas</h1>
    </header>

    <main id="app" role="main">
      <input
        type="text"
        v-model="filtroNombre"
        placeholder="Buscar por nombre o apellido..."
        aria-label="Filtro por nombre o apellido"
      />
      <input
        type="text"
        v-model="filtroDni"
        placeholder="Buscar por DNI..."
        aria-label="Filtro por DNI"
      />

      <div v-if="mostrarAdvertencia" class="alert" role="alert">
        Ingrese al menos 3 caracteres en alguno de los filtros.
      </div>

      <section aria-live="polite" aria-atomic="true">
        <transition-group name="fade" tag="div">
          <article
            class="card"
            v-for="persona in personasFiltradas"
            :key="persona.dni"
            tabindex="0"
          >
            <h5>{{ getNombreCompleto(persona) }}</h5>
            <p>DNI: {{ persona.dni }}</p>
            <a :href="`mailto:${persona.correo}`">{{ persona.correo }}</a>
          </article>
        </transition-group>
        <p v-if="personasFiltradas.length === 0">
          No se encontraron resultados.
        </p>
      </section>
    </main>

    <footer>
      <p>© Gastón Kopplin Alva - Trabajo Práctico 2 - PNT2</p>
    </footer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      filtroNombre: "",
      filtroDni: "",
      buscando: false,
      personas: [
        {
          nombre: "Daniel",
          apellido: "Sanchez",
          correo: "danielsanchez68@hotmail.com",
          dni: "20442873",
        },
        {
          nombre: "Juan",
          apellido: "Perez",
          correo: "j@p.gmail.com",
          dni: "12345678",
        },
        {
          nombre: "Ana",
          apellido: "Suarez",
          correo: "a@s.gmail.com",
          dni: "87654321",
        },
        {
          nombre: "Gasti",
          apellido: "Kopp",
          correo: "kopplinkopplin@gmail.com",
          dni: "41555555",
        },
      ],
    };
  },
  computed: {
    personasFiltradas() {
      const filtroNombreValido = this.filtroNombre.length >= 3;
      const filtroDniValido = this.filtroDni.length >= 3;

      this.buscando = filtroNombreValido || filtroDniValido;

      if (!filtroNombreValido && !filtroDniValido) {
        return this.personas;
      }

      return this.personas.filter((persona) => {
        const nombreCompleto = (
          persona.nombre +
          " " +
          persona.apellido
        ).toLowerCase();
        const dni = persona.dni.toLowerCase();
        const filtroNombreLower = this.filtroNombre.toLowerCase();
        const filtroDniLower = this.filtroDni.toLowerCase();

        const cumpleFiltroNombre = filtroNombreValido
          ? nombreCompleto.includes(filtroNombreLower)
          : true;
        const cumpleFiltroDni = filtroDniValido
          ? dni.includes(filtroDniLower)
          : true;

        return cumpleFiltroNombre && cumpleFiltroDni;
      });
    },
    mostrarAdvertencia() {
      return (
        (this.filtroNombre.length > 0 && this.filtroNombre.length < 3) ||
        (this.filtroDni.length > 0 && this.filtroDni.length < 3)
      );
    },
  },
  methods: {
    getNombreCompleto(persona) {
      return `${persona.nombre} ${persona.apellido}`;
    },
    
  },
  watch: {
    personasFiltradas() {
      this.$nextTick(() => {
        const cards = document.querySelectorAll(".card");

        if (this.buscando) {
          cards.forEach((card) => {
            card.classList.remove("show", "move-up");
            void card.offsetWidth;
            card.classList.add("show");
          });

          setTimeout(() => {
            cards.forEach((card) => {
              card.classList.add("move-up");
            });
          }, 600);
        } else {
          cards.forEach((card) => {
            card.classList.remove("show", "move-up");
            card.style.transform = "";
            void card.offsetWidth;
          });
        }
      });
    },
  },
};
</script>
