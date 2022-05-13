<style>
</style>
<script setup>
import { ref } from 'vue';
const carrinho = {};
const produtos = {
  0: { Img: "", Nome: "Ps Vita", Val: 157.59, refe:ref(false)},
  1: { Img: "", Nome: "Ps 4", Val: 3000, refe:ref(false)},
  2: { Img: "", Nome: "Ps 5", Val: 4999.99, refe:ref(false)},
  3: { Img: "", Nome: "Ps 1", Val: 600.50, refe:ref(false)},
  4: { Img: "", Nome: "MegaDrive", Val: 25.65, refe:ref(false)},
  5: { Img: "", Nome: "Polystation", Val: 60, refe:ref(false)},
  6: { Img: "", Nome: "Fone XRL", Val: 79.99, refe:ref(false)},
  7: { Img: "", Nome: "JBS Caixa", Val: 78, refe:ref(false)},
  8: { Img: "", Nome: "Micro Fone", Val: 25, refe:ref(false)},
  9: { Img: "", Nome: "Suporte celular", Val: 50, refe:ref(false)},
  10: { Img: "", Nome: "Iphone", Val: 4000, refe:ref(false)},
  11: { Img: "", Nome: "Andorid 10", Val: 0, refe:ref(false)}
};



//Exibir contador
const count_car = ref(0);

//Evitar processamento de elementos que nao esta visivel
const modal = ref(false);

//Verifica se ja existe na lista
async function prod_exist(CodProd) {
  return Object.keys(carrinho).includes(CodProd);
}

//Adicionar produto ao carrinho
async function Add_prod(CodProd) {
  //Produto ja existe?
  if (await prod_exist(CodProd)) {
    carrinho[CodProd].value += 1;
  } else {
    carrinho[CodProd] = ref(1);
    produtos[CodProd].refe.value=true;
  }
  
  count_car.value = Object.keys(carrinho).length;
}

//Remove produto do carrinho
async function Remov_prod(CodProd) {
  delete carrinho[CodProd];
  produtos[CodProd].refe.value=false;
  count_car.value = Object.keys(carrinho).length;
}

//Processar itens no carrinho
function showCart() {
  $("#carrinho").modal("show");
  modal.value = true;

}


//OBS:Tambem pode ser usado mounted, direto do vueJs
$(document).ready(function () {
  //Adiciona um callback ao fechar MODAL: carrinho
  $('#carrinho').on('hidden.bs.modal', function (e) {
    // call your method
    modal.value = false;
  });
});

//Habilita opcao do usuario inserir a quantidade direto no inpupt do produto
function cart_QtdUpdate(e){
  //13 = ENTER
  if(e.keyCode == 13){
    //Verifica Qtd
    if($(this).val()>0){
      carrinho[$(this).data("codprod")].value=$(this).val();
    }else{
      //Qtd < 0 : Remove produto
      Remov_prod($(this).data("codprod"));
    }
  }
  console.info(e)
}
</script>

<template>
  <!-- NAVBAR: Lendo documentaçao do VueJs, vi que recomendam picar areas em outros arquivos -->
  <nav class="navbar navbar-light bg-light">
    <div class="container-fluid">
      <a class="navbar-brand">Exercicio</a>
      <div class="position-relative" style="margin-right: 25px;">
        <button @click="showCart()" type="button" class="btn btn-primary position-relative">
          Carrinho
          <span v-if="count_car > 0"
            class="position-absolute top-0 start-100 translate-middle badge rounded-pill bg-danger">
            {{ count_car }}
          </span>
        </button>
      </div>
    </div>
  </nav>
  <!-- CORPO PRINCIPAL -->
  <div class="container">
    <div class="row mt-3">
      <div class="col-3 mb-1" v-for="key in Object.keys(produtos)">
        <div class="card" style="width: 18rem;">
          <img v-bind:src="'src/assets/' + key + '.jpg'" class="card-img-top" alt="...">
          <div class="card-body">
            <h5 class="card-title">{{ produtos[key].Nome }}</h5>
            <h5 class="card-title">R${{ produtos[key].Val }}</h5>
            <h5 v-if="produtos[key].refe.value" class="card-subtitle">Carrinho: {{carrinho[key]}}</h5>
            <a @click="Add_prod(key)" v-bind:data-codprod="key"
              v-bind:class="+produtos[key].refe.value ? 'btn btn-primary w-100' : 'btn btn-success w-100'"
              >{{produtos[key].refe.value ? 'Adicionar +1' : 'Comprar'}}</a>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- MODAL: Carrinho de compras, todos itens e total -->
  <div class="modal fade" id="carrinho" tabindex="-1" aria-labelledby="exampleModalLabel"
    aria-hidden="true">
    <div class="modal-dialog modal-lg">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">Carrinho</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <ul v-if="modal" class="list-group list-group-flush">
            <li v-for="key in Object.keys(carrinho)"
              class="list-group-item d-flex justify-content-between align-items-center">
              <img v-bind:src="'src/assets/' + key + '.jpg'" alt="" srcset="">
              <div class="input-group w-50">
                <button @click="carrinho[key].value>1 ? carrinho[key].value-- : Remov_prod(key)" class="btn btn-outline-secondary" type="button" id="inputGroupFileAddon04">-</button>
                <input v-bind:onkeyup="cart_QtdUpdate" v-bind:data-codprod="key" type="number" min="0" class="form-control" id="inputGroupFile04" v-bind:value="carrinho[key].value">
                <button @click="carrinho[key].value++" class="btn btn-outline-secondary" type="button" id="inputGroupFileAddon04">+</button>
                <button @click="Remov_prod(key)" class="btn btn-outline-danger" type="button" id="inputGroupFileAddon04">Remove</button>
              </div>
              <span>{{ produtos[key].Nome }}</span>
            </li>
          </ul>
          <span>Caso quantidade do produto chegue a 0 ele sera removido do carrinho, você tambem pode alterar quantidade inserindo direto o valor e pressionando ENTER</span>
        </div>
        <div class="modal-footer">
          <h1 class="w-100 text-center">R${{Object.keys(carrinho).reduce((a,v)=> a + (produtos[v].Val * carrinho[v].value),0).toFixed(2)}}</h1>
        </div>
      </div>
    </div>
  </div>
</template>
<style>
#carrinho img {
  width: 100px;
}
</style>