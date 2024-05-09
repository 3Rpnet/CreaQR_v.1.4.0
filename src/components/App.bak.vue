<template>
  <div>
    <b-navbar style="border-bottom: 1px solid #000000">
      <template #start>
        <b-navbar-item><strong>TeamTechCL - Productos y Servicios Tecnológicos -  Generador de código QR</strong></b-navbar-item>
      </template>
      <template #end>
        <b-navbar-item tag="div">
          <div class="buttons">
            <a href="https://teamtech.cl/#contacto" class="button is-dark">
              <b-icon icon="help"></b-icon>
              <strong>Soporte</strong>
            </a>
          </div>
        </b-navbar-item>
      </template>
    </b-navbar>

    <!--codigo input agregados -->
    <section class="p-2">
      <div class="columns">
         <div class="column is-one-quarter">
            <b-field label="Parametros" horizontal
              type="is-success">
                <b-input maxlength="10" icon-right="close-circle"></b-input>
            </b-field>
          </div>
      </div>      
      
      <article>
        <b-field>
          <b-input placeholder="Email"
              v-model="email"
              type="email"
              icon="email"
              icon-right="close-circle"
              icon-right-clickable
              @icon-right-click="clearIconClick">
          </b-input>
      </b-field>
      </article>
    </section>

    <!--fin input -->

    <section class="p-2">
      <div class="columns">
        <div class="column">
          <b-field
            label="Contenido"
            message="Soporta cualquier tipo de texto alfanumérico."
          >
            <b-input
              @keyup.native="actualizarCodigoQr()"
              v-model="detallesQr.value"
              ref="textareaContenido"
              type="textarea">
            </b-input>
          </b-field>
          <b-field grouped>
            <b-field label="Corrección">
              <b-select
                @input="actualizarCodigoQr()"
                placeholder="Seleccione"
                v-model="detallesQr.level"
              >
                <option value="L">Baja</option>
                <option value="M">Media</option>
                <option value="Q">Alta</option>
                <option value="H">Máxima</option>
              </b-select>
            </b-field>
            <b-field
              label="Tamaño de imagen"
              message="El tamaño seleccionado se reflejará en la descarga"
            >
              <b-slider
                v-model="detallesQr.size"
                :min="10"
                @change="actualizarCodigoQr()"
                :max="1000"
              ></b-slider>
            </b-field>
          </b-field>
          <b-field grouped>
            <b-field label="Color de código">
              <SeleccionadorColor
                @input="onColorCambiado"
                :value="colores"
              ></SeleccionadorColor>
            </b-field>

            <b-field label="Color de fondo">
              <SeleccionadorColor
                @input="onColorDeFondoCambiado"
                :value="coloresFondo"
              ></SeleccionadorColor>
            </b-field>
            <b-field label="Opacidad del fondo">
              <b-slider
                v-model="detallesQr.backgroundAlpha"
                :min="0"
                :step="0.1"
                @change="actualizarCodigoQr()"
                :max="1"
              ></b-slider>
            </b-field>
          </b-field>
        </div>
        <div class="column is-one-third">
          <div class="card has-text-centered">
            <header class="card-header">
              <p class="card-header-title">Vista previa</p>
            </header>
            <div class="card-content">
              <div class="content">
                <img alt="Código QR" id="codigo" />
                <br />
                <b-button @click="descargar()" type="is-success"
                  >Descargar ahora</b-button
                >
              </div>
            </div>
          </div>
          <!--<br />-->
          <div class="card has-text-centered">
            <div class="card-content">Texto prueba</div>
          </div>
        </div>
      </div>
      <b-notification type="is-info" :closable="false">
        Generador de códigos QR personalizados, soporta cualquier tipo de texto o números. configura el tamaño, color de texto y fondo, calidad y transparencia
      </b-notification>
    </section>
    <footer class="footer">
      <div class="content has-text-centered">
        <p>
          <strong>Genera QR  -  </strong>
          <b-icon icon="heart" type="is-danger"></b-icon>por
          <a href="https://teamtech.cl/">- TeamTechCL -</a>
        </p>
      </div>
    </footer>
  </div>
</template>

<script>
const colorPorDefecto = "#000000",
  colorDeFondoPorDefecto = "#ffffff",
  nivelCorrecionAlto = "H";
import Logo from "./assets/teamtech.png";
import QRious from "qrious";
import { Sketch } from "vue-color";
export default {
  components: {
    SeleccionadorColor: Sketch,
  },
  name: "app",
  data: () => ({
    logo: Logo,
    detallesQr: {
      value: "",
      foreground: colorPorDefecto,
      background: colorDeFondoPorDefecto,
      size: 200,
      level: nivelCorrecionAlto,
    },
    qr: null,
    colores: {
      hex: colorPorDefecto,
    },
    coloresFondo: {
      hex: colorDeFondoPorDefecto,
    },
  }),
  methods: {
    descargar() {
      const a = document.createElement("a");
      a.href = document.querySelector("#codigo").src;
      a.download = "Código QR.png";
      a.click();
    },
    actualizarCodigoQr() {
      this.qr.set({
        value: this.detallesQr.value,
        foreground: this.detallesQr.foreground,
        background: this.detallesQr.background,
        size: this.detallesQr.size,
        backgroundAlpha: this.detallesQr.backgroundAlpha,
        level: this.detallesQr.level,
      });
    },
    onColorCambiado(nuevoColor) {
      this.detallesQr.foreground = nuevoColor.hex;
      this.actualizarCodigoQr();
    },
    onColorDeFondoCambiado(nuevoColor) {
      this.detallesQr.background = nuevoColor.hex;
      this.actualizarCodigoQr();
    },
  },
  mounted() {
    this.qr = new QRious({
      element: document.querySelector("#codigo"),
    });
    this.actualizarCodigoQr();
    this.$refs.textareaContenido.focus();
  },
};
</script>