<template>
  <div>
    <div id="container"></div>
    <div id="sidebar"></div>
    <div id="menu"></div>
  </div>
</template>

<script>
import mxgraph from "mxgraph";

//se você planeja usar interfaces integradas, precisa especificar caminhos para recursos
const graphConfig = {
  mxBasePath: "/mx/", //Especifica o caminho em mxClient.basePath.
  mxImageBasePath: "/mx/images", //Especifica o caminho em mxClient.imageBasePath.
  mxLanguage: "en", //Especifica o idioma para recursos em mxClient.language.
  mxDefaultLanguage: "en", //Especifica o idioma padrão em mxClient.defaultLanguage.
  mxLoadResources: false, //Especifica se algum recurso deve ser carregado. O padrão é verdadeiro.
  mxLoadStylesheets: false, //Especifica se alguma folha de estilo deve ser carregada. Padrão é verdadeiro
};

const {
  mxClient,
  mxUtils,
  mxEvent,
  mxEditor,
  mxRectangle,
  mxImageShape,
  mxGraph,
  mxGeometry,
  mxCell,
  mxGraphModel, //novo
  mxConstants,
  mxImage,
  mxDivResizer,
  mxObjectCodec,
  mxCodecRegistry,
  mxConnectionHandler,
} = mxgraph(graphConfig);

window.mxClient = mxClient;
window.mxUtils = mxUtils;
window.mxRectangle = mxRectangle;
window.mxImageShape = mxImageShape;
window.mxGraph = mxGraph;
window.mxEvent = mxEvent;
window.mxCell = mxCell;
window.mxGraphModel = mxGraphModel;
window.mxConstants = mxConstants;
window.mxGeometry = mxGeometry;
window.mxImage = mxImage;
window.mxEditor = mxEditor;
window.mxDivResizer = mxDivResizer;
window.mxObjectCodec = mxObjectCodec;
window.mxCodecRegistry = mxCodecRegistry;
window.mxConnectionHandler = mxConnectionHandler;

var editor;

// CustomUserObject
// Objeto de usuário personalizado - empresa
// window.Empresa = function (name) {
//   this.name = name = "Tipo";
//   this.clone = function () {
//     return mxUtils.clone(this);
//   };
// };

window.Cliente = function (name) {
  this.name = name = "Tipo";
  this.clone = function () {
    return mxUtils.clone(this);
  };
};

export default {
  name: "Modelagem",
  data() {
    return {
      currentCell: null,
    };
  },

  methods: {
    // altera o selecionado em um painel separado
    // e transfere o objeto para o ambiente VUE
    selectionChanged() {
      let cell = editor.graph.getSelectionCell();
      this.$set(this, "currentCell", cell);
    },

    // retorna o valor atualizado para o ambiente mxGraph
    changeObjectValues(newCellValue) {
      editor.graph.model.setValue(this.currentCell, newCellValue);
    },

    // adiciona o elementos a ser arrastado ao menu lateral
    addSidebarIcon: function (graph, sidebar, prototype) {
      let funct = function (graph, evt) {
        graph.stopEditing(false);

        let pt = graph.getPointForEvent(evt);

        let parent = graph.getDefaultParent();
        let model = graph.getModel();

        let v1 = model.cloneCell(prototype);

        model.beginUpdate();

        try {
          v1.geometry.x = pt.x;
          v1.geometry.y = pt.y;
          //v1.style = editor.graph.stylesheet.getDefaultEdgeStyle();

          v1.geometry.alternateBounds = new mxRectangle(
            0,
            0,
            v1.geometry.width,
            v1.geometry.height,
            ""
          );

          // v1.geometry.alternateBounds = new mxImage(
          //   require("../assets/handle-connect.png"),
          //   108,
          //   108,
          //   v1.geometry.width,
          //   v1.geometry.height,
          //   ""
          // );

          graph.addCell(v1, parent);
        } finally {
          model.endUpdate();
        }

        graph.setSelectionCell(v1);
      };

      // =================================================================================================================
      //Cria a imagem que serve de ícone da barra lateral (fonte de arrastar) - agregador
      let texto = document.createElement("div");
      texto.style.cursor = "pointer";
      texto.style.backgroundColor = "transparent";
      //texto.style.webkitTransform = "skew(20deg)";
      texto.style.margin = "25px";
      texto.style.width = "200px";
      texto.style.height = "60px";
      texto.style.textAlign = "center";
      texto.style.display = "flex";
      texto.style.flexWrap = "wrap";
      texto.style.alignItems = "center";
      texto.style.justifyContent = "center";
      texto.innerHTML = '<div style="color: #00000;">Texto... texto</div>';
      sidebar.appendChild(texto);

      // Creates the image which is used as the drag icon (preview)
      let dragTexto = texto.cloneNode(true);
      mxUtils.makeDraggable(texto, graph, funct, dragTexto);

      // =================================================================================================================
      //Cria a imagem que serve de ícone da barra lateral (fonte de arrastar) - agregador
      let agregador = document.createElement("div");
      agregador.style.cursor = "pointer";
      agregador.style.backgroundColor = "red";
      agregador.style.webkitTransform = "skew(20deg)";
      agregador.style.margin = "25px";
      agregador.style.width = "200px";
      agregador.style.height = "60px";
      agregador.style.textAlign = "center";
      agregador.style.display = "flex";
      agregador.style.flexWrap = "wrap";
      agregador.style.alignItems = "center";
      agregador.style.justifyContent = "center";
      agregador.innerHTML =
        '<div style="color: #00000; -webkit-transform: skew(-20deg); -moz-transform: skew(-20deg); -o-transform: skew(-20deg); "><strong>Agregador</strong></div>';
      sidebar.appendChild(agregador);

      // Creates the image which is used as the drag icon (preview)
      let dragAgregador = agregador.cloneNode(true);
      mxUtils.makeDraggable(agregador, graph, funct, dragAgregador);

      // =================================================================================================================
      //Cria a imagem que serve de ícone da barra lateral (fonte de arrastar) - Cliente do cliente
      let clienteDoCliente = document.createElement("div");
      clienteDoCliente.style.cursor = "pointer";
      //clienteDoCliente.style.backgroundColor = "gray";
      clienteDoCliente.style.background = `url("${require("../assets/teste.png")}")`;
      clienteDoCliente.style.margin = "25px";
      clienteDoCliente.style.width = "200px";
      clienteDoCliente.style.height = "60px";
      clienteDoCliente.style.textAlign = "center";
      clienteDoCliente.style.display = "flex";
      clienteDoCliente.style.flexWrap = "wrap";
      clienteDoCliente.style.alignItems = "center";
      clienteDoCliente.style.justifyContent = "center";
      clienteDoCliente.innerHTML =
        '<div style="color: #00000;"><strong>Cliente do Cliente</strong></div>';
      sidebar.appendChild(clienteDoCliente);

      // Creates the image which is used as the drag icon (preview)
      let dragClienteDoCliente = clienteDoCliente.cloneNode(true);
      mxUtils.makeDraggable(
        clienteDoCliente,
        graph,
        funct,
        dragClienteDoCliente
      );
      // =================================================================================================================
      //Cria a imagem que serve de ícone da barra lateral (fonte de arrastar) - intermediario
      let intermediario = document.createElement("div");
      intermediario.style.cursor = "pointer";
      //intermediario.style.backgroundColor = "green";
      intermediario.style.background = `url("${require("../assets/intermediario.png")}")`;
      intermediario.style.margin = "25px";
      intermediario.style.width = "200px";
      intermediario.style.height = "60px";
      intermediario.style.textAlign = "center";
      intermediario.style.display = "flex";
      intermediario.style.flexWrap = "wrap";
      intermediario.style.alignItems = "center";
      intermediario.style.justifyContent = "center";
      intermediario.innerHTML =
        '<div style="color: #00000;" ><strong>Intermediário</strong></div>';
      sidebar.appendChild(intermediario);

      // Creates the image which is used as the drag icon (preview)
      let dragIntermediario = intermediario.cloneNode(true);
      mxUtils.makeDraggable(intermediario, graph, funct, dragIntermediario);

      //  // =================================================================================================================
      //Cria a imagem que serve de ícone da barra lateral (fonte de arrastar) - cliente
      let cliente = document.createElement("div");
      cliente.style.cursor = "pointer";
      //cliente.style.backgroundColor = "yellow";
      cliente.style.background = `url("${require("../assets/cliente.png")}")`;
      cliente.style.margin = "25px";
      cliente.style.width = "200px";
      cliente.style.height = "60px";
      cliente.style.textAlign = "center";
      cliente.style.display = "flex";
      cliente.style.flexWrap = "wrap";
      cliente.style.alignItems = "center";
      cliente.style.justifyContent = "center";
      cliente.innerHTML =
        '<div style="color: #00000"><strong>Cliente</strong></div>';
      sidebar.appendChild(cliente);

      // Creates the image which is used as the drag icon (preview)
      let dragCliente = cliente.cloneNode(true);
      mxUtils.makeDraggable(cliente, graph, funct, dragCliente);

      // =================================================================================================================
      //Cria a imagem que serve de ícone da barra lateral (fonte de arrastar) - fornecedor
      let fornecedor = document.createElement("div");
      fornecedor.style.cursor = "pointer";
      //fornecedor.style.backgroundColor = "#FFA500";
      fornecedor.style.background = `url("${require("../assets/fornecedor.png")}")`;
      fornecedor.style.margin = "25px";
      fornecedor.style.width = "200px";
      fornecedor.style.height = "60px";
      fornecedor.style.textAlign = "center";
      fornecedor.style.display = "flex";
      fornecedor.style.flexWrap = "wrap";
      fornecedor.style.alignItems = "center";
      fornecedor.style.justifyContent = "center";
      fornecedor.innerHTML =
        '<div style="color: #00000"><strong>Fornecedor</strong></div>';
      sidebar.appendChild(fornecedor);

      // Creates the image which is used as the drag icon (preview)
      let dragFornecedor = fornecedor.cloneNode(true);
      mxUtils.makeDraggable(fornecedor, graph, funct, dragFornecedor);

      // =================================================================================================================
      //Cria a imagem que serve de ícone da barra lateral (fonte de arrastar) - empresa de interesse
      let empresa = document.createElement("div");
      empresa.style.cursor = "pointer";
      empresa.style.backgroundColor = "blue";
      //empresa.style.background = `url("${require("../public/mx/images/warning.png")}")`;
      empresa.style.margin = "25px";
      empresa.style.width = "200px";
      empresa.style.height = "60px";
      empresa.style.textAlign = "center";
      empresa.style.display = "flex";
      empresa.style.flexWrap = "wrap";
      empresa.style.alignItems = "center";
      empresa.style.justifyContent = "center";
      empresa.innerHTML =
        '<div style="color: #F8F8FF"><strong>Empresa de Interesse</strong></div>';
      sidebar.appendChild(empresa);

      // Cria a imagem que é usada como ícone de arrastar (visualização)
      let dragEmpresa = empresa.cloneNode(true);
      mxUtils.makeDraggable(empresa, graph, funct, dragEmpresa);

      //OBS.: mudar os icones para imagens já defenidas na notação
    },

    // cria e configura o editor
    createGraph() {
      // Verifica se o navegador é compatível
      if (!mxClient.isBrowserSupported()) {
        // Exibe uma mensagem de erro se o navegador não for compatível.
        mxUtils.error("Navegador não suportado!", 200, false);
      } else {
        /// mxConnectionHandler.prototype.connectImage = new mxImage(
        //   require("../assets/handle-connect.png"),
        //   18,
        //   18
        // );

        let menu = document.getElementById("menu");
        menu.style.position = "absolute";
        menu.style.overflow = "hidden";
        menu.style.height = "50px";
        menu.style.width = "1862px";
        menu.style.top = "0px";
        menu.style.left = "0px";
        menu.style.right = "0px";
        menu.style.bottom = "0px";
        menu.style.backgroundColor = "#5A5A5A";

        let container = document.getElementById("container");
        container.style.position = "absolute";
        container.style.overflow = "hidden";
        container.style.height = "825px";
        container.style.width = "1500px";
        container.style.top = "80px";
        container.style.left = "320px";
        container.style.right = "0px";
        container.style.bottom = "40px";
        container.style.background = `url("${require("../assets/grid.gif")}")`;

        let sidebar = document.getElementById("sidebar");
        sidebar.style.position = "absolute";
        sidebar.style.overflow = "hidden";
        sidebar.style.padding = "20px";
        sidebar.style.left = "20px";
        sidebar.style.top = "80px";
        sidebar.style.width = "230px";
        sidebar.style.height = "785px";
        sidebar.style.bottom = "40px";
        sidebar.style.display = "flex";
        sidebar.style.flexDirection = "column-reverse";
        sidebar.style.alignItems = "center";
        sidebar.style.justifyContent = "flex-end";
        sidebar.style.backgroundColor = "#F0F0F0";

        if (mxClient.IS_QUIRKS) {
          document.body.style.overflow = "hidden";
          new mxDivResizer(container);
          new mxDivResizer(sidebar);
          new mxDivResizer(menu);
        }

        editor = new mxEditor();
        editor.setGraphContainer(container);

        // gerenciamento de conexão global
        editor.graph.setConnectable(true);
        editor.graph.setEnabled(true); // novo
        editor.graph.setTooltips(true); //novo
        editor.graph.setCellsDisconnectable(true);
        editor.graph.setPanning(true);
        editor.graph.setAllowDanglingEdges(false);
        //editor.graph.setTextContent(true);

        // altera o selecionado em um painel separado
        editor.graph.getSelectionModel().addListener(mxEvent.CHANGE, () => {
          this.selectionChanged();
        });
        this.selectionChanged();

        editor.graph.centerZoom = true;
        editor.graph.swimlaneNesting = false;
        editor.graph.dropEnabled = true;

        //Os campos são rótulos HTML criados dinamicamente
        // editor.graph.isHtmlLabel = function (cell) {
        //   return !this.isSwimlane(cell) && !this.model.isEdge(cell);
        // };

        // gráfico editável
        editor.graph.isCellEditable = function () {
          return true;
        };

        // remover grafico
        editor.graph.removeFromParent = function () {
          return true;
        };

        // mxCell.prototype.remove = function (index) {
        //   return mxGraph.prototype.remove.apply(index);
        // };

        // Retorna a propriedade do nome do objeto de usuário para o rótulo
        editor.graph.convertValueToString = function (cell) {
          if (cell.value != null && cell.value.name != null) {
            return cell.value.name;
          }
          return mxGraph.prototype.convertValueToString.apply(this, arguments); // "supercall"
        };

        // // Cria um rótulo HTML dinâmico para propriedades
        // editor.graph.getLabel = function (cell) {
        //   console.log("getLabel ", cell.value.name);
        //   if (cell && this.isHtmlLabel(cell) && cell.value) {
        //     let label = "";
        //     label +=
        //       '<div style="width: 100%; display: flex; justify-content: space-between; align-items: center"; >';
        //     label +=
        //       '<div style="width: 100px; margin-left: 10px; font-weight: 600"; >' +
        //       "<strong>" +
        //       mxUtils.htmlEntities(cell.value.name, true) +
        //       "</strong>" +
        //       "</div>";

        //     label += "</div>";

        //     return label;
        //   }

        //   return mxGraph.prototype.getLabel.apply(this, arguments); // "supercall"
        // };

        // Adiciona o ícone da barra lateral para o objeto de propriedade
        let customObject = new window.Cliente();

        let object = new mxCell(
          customObject,
          new mxGeometry(0, 0, 200, 60),
          "fillColor=blue;strokeColor=white;fontColor=white;"
          // new image para passar a imagem ao invés de um objeto retangular
        );
        object.setVertex(true);
        object.setConnectable(true);
        //mxCell.prototype.removeFromParent = function();
        //console.log(object);
        this.addSidebarIcon(editor.graph, sidebar, object);
      }
    },
    // configurações
    init() {
      // CÓDIGOS DOS CAMPOS PERSONALIZADOS para importação e exportação
      let codecCustomUserObject = new mxObjectCodec(
        new window.CustomUserObject()
      );

      // codificar
      codecCustomUserObject.encode = function (enc, obj) {
        let node = enc.document.createElement("Nome");
        mxUtils.setTextContent(node, JSON.stringify(obj));
        console.log(node);
        return node;
      };

      //decodificar
      codecCustomUserObject.decode = function (dec, node) {
        let obj = JSON.parse(mxUtils.getTextContent(node));
        let beatyObj = new window.CustomUserObject();
        obj = Object.assign(beatyObj, obj);
        return obj;
      };

      mxCodecRegistry.register(codecCustomUserObject);

      // chama o metodo criador do grafico
      this.createGraph();
    },
  },
  mounted() {
    // chama o metodo init
    this.init();
  },
};
</script>