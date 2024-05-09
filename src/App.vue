<template>
  <div>
    <b-navbar style="border-bottom: 1px solid #000000">
      <template #start>
        <b-navbar-item><strong>TeamTechCL - Productos y Servicios Tecnológicos</strong>
          <span style="color:brown; margin-left:200px"> App - <strong> Generador de código QR</strong></span></b-navbar-item>
      </template>
      <template #end>
        <b-navbar-item tag="div">
          <div class="buttons">
            <a href="https://teamtech.cl/#contacto" class="button is-dark">
              <strong>Soporte</strong>
              <b-icon icon="help"></b-icon>
            </a>
          </div>
        </b-navbar-item>
      </template>
    </b-navbar>
  
    <section class="p-2">
      <div class="columns">

        <div class="column is-one-third">
          <b-field label="Dominio" horizontal
            type="is-success">            
              <b-input        
                v-model="form.Texto1"
                @keyup.native="updateTexto()"
                ref="textoarea1"
                type="text"
                id="texto1"
                icon-right="close-circle"
                icon-right-clickable
                @icon-right-click="limpiaTexto1()"                            
              ></b-input>
          </b-field>          
        </div>       
        
        <div class="column is-one-third">
          <b-field label="Carpeta/subcarpeta" horizontal
            type="is-success">
              <b-input
                v-model="form.Texto2"
                @keyup.native="updateTexto()"
                type="text"
                id="texto2"
                icon-right="close-circle"
                icon-right-clickable
                @icon-right-click="limpiaTexto2()"                
              ></b-input>
          </b-field>
        </div>

        <div class="column">
          <b-field label="Archivo" horizontal
            type="is-success">
              <b-input
                v-model="form.Texto3"
                @keyup.native="updateTexto()"
                type="text"
                id="texto3"
                icon-right="close-circle"
                icon-right-clickable
                @icon-right-click="limpiaTexto3()"               
              ></b-input>
          </b-field>
        </div>
      </div>
      
       <div v-show="true">
        <button @click="enviaFoco()" type="is-success">
          Generar QR
        </button>
        <button style="margin-left:5px" @click="actualizar()" type="is-success">
          Refresh
        </button>
      </div> 
      
      <div class="columns">
        <div class="column is-half">
          <b-field             
            message="Soporta cualquier tipo de texto alfanumérico."
          >            
            <b-input
              @keyup.native="actualizarCodigoQr()"
              v-model="detallesQr.value"              
              type="textarea"
              id="textoFinal"
              ref="textareaContenido"                      
            ></b-input>

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
            <b-field style="color:darkblue"
              label="Tamaño de imagen"
              message="El tamaño seleccionado se reflejará en la descarga"
            >
              <b-slider                 
                v-model="detallesQr.size"
                :min="10"
                @change="actualizarCodigoQr()"
                :max="1000"
                type="is-info"
              ></b-slider>
            </b-field>
          </b-field>

  </div>

        <div>
          <b-field label="Color de código">
            <SeleccionadorColor
              @input="onColorCambiado"
              :value="colores"
            ></SeleccionadorColor>
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
         
        </div>
      </div>
    
    </section>
    <footer class="footer">
      <div class="content has-text-centered">
        <p>
          <strong style="color:darkblue">Genera QR  APP</strong>
          <b-icon icon="infinity" type="is-danger"></b-icon>
          <a href="https://teamtech.cl/"> TeamTechCL </a>
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
        form: {
          Texto1:'',
          Texto2:'',
          Texto3:'',
          Textofull:''
        },
        
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
      enviaFoco(){
        this.actualizarCodigoQr();
      },
      actualizar() {
        location.reload();
      },
      limpiaTexto1() {
        this.form.Texto1=""
      },
      limpiaTexto2() {
        this.form.Texto2=""
      },
      limpiaTexto3() {
        this.form.Texto3=""
      },

      updateTexto() { 
        if (this.form.Texto1 !== ""){
          this.form.Textofull = this.form.Texto1;
          this.detallesQr.value = this.form.Texto1;
        }       
        if (this.form.Texto2 !== ""){
          this.form.Textofull = this.form.Texto1 + "/" + this.form.Texto2 + "/";
          this.detallesQr.value = this.form.Texto1 + "/" + this.form.Texto2 + "/";
        }
          if (this.form.Texto3 !== ""){
            this.form.Textofull = this.form.Texto1 + "/"+ this.form.Texto2 + "/" + this.form.Texto3;
            this.detallesQr.value = this.form.Texto1 + "/"+ this.form.Texto2 + "/" + this.form.Texto3;
          }        
      
      },

      descargar() {
        const a = document.createElement("a");
        a.href = document.querySelector("#codigo").src;
        a.download = "Código_QR.png";
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
      this.$refs.textoarea1.focus();

      },
  }
  
</script>