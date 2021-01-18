<template>
  <div>
    <div id="container"></div>
    <div id="sidebar"></div>
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
// Objeto de usuário personalizado
window.Empresa = function (name) {
  this.name = name = "Empresa";
  this.clone = function () {
    return mxUtils.clone(this);
  };
};

export default {
  name: "Empresa",
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
          v1.style = editor.graph.stylesheet.getDefaultEdgeStyle();
          v1.geometry.alternateBounds = new mxRectangle(
            0,
            0,
            v1.geometry.width,
            v1.geometry.height,
            ""
          );

          graph.addCell(v1, parent);
        } finally {
          model.endUpdate();
        }

        graph.setSelectionCell(v1);
      };

      //Cria a imagem que serve de ícone da barra lateral (fonte de arrastar) - empresa de interesse
      let wrapper = document.createElement("div");
      wrapper.style.cursor = "pointer";
      wrapper.style.backgroundColor = "blue";
      wrapper.style.margin = "10px";
      wrapper.style.width = "200px";
      wrapper.style.height = "60px";
      wrapper.style.textAlign = "center";
      wrapper.style.display = "flex";
      wrapper.style.flexWrap = "wrap";
      wrapper.style.alignItems = "center";
      wrapper.style.justifyContent = "center";
      wrapper.innerHTML =
        '<div style="color: #F8F8FF"><strong>Empresa de Interesse</strong></div>';
      sidebar.appendChild(wrapper);

      // Cria a imagem que é usada como ícone de arrastar (visualização)
      let dragImage = wrapper.cloneNode(true);
      mxUtils.makeDraggable(wrapper, graph, funct, dragImage);

      //OBS.: mudar os icones para imagens já defenidas na notação
    },

    // cria e configura o editor
    createGraph() {
      // Verifica se o navegador é compatível
      if (!mxClient.isBrowserSupported()) {
        // Exibe uma mensagem de erro se o navegador não for compatível.
        mxUtils.error("Navegador não suportado!", 200, false);
      } else {
        // mxConnectionHandler.prototype.connectImage = new mxImage(
        //   require("../assets/handle-connect.png"),
        //   18,
        //   18
        // );

        let container = document.getElementById("container");
        container.style.position = "absolute";
        container.style.overflow = "hidden";
        container.style.left = "250px";
        container.style.top = "0px";
        container.style.right = "0px";
        container.style.bottom = "0px";
        container.style.background = `url("${require("../assets/grid.gif")}")`;

        let sidebar = document.getElementById("sidebar");
        sidebar.style.position = "absolute";
        sidebar.style.overflow = "hidden";
        sidebar.style.padding = "20px";
        sidebar.style.left = "0px";
        sidebar.style.top = "0px";
        sidebar.style.width = "250px";
        sidebar.style.bottom = "0px";
        sidebar.style.display = "flex";
        sidebar.style.flexDirection = "column-reverse";
        sidebar.style.alignItems = "center";
        sidebar.style.justifyContent = "flex-end";
        sidebar.style.backgroundColor = "#292961";

        if (mxClient.IS_QUIRKS) {
          document.body.style.overflow = "hidden";
          new mxDivResizer(container);
          new mxDivResizer(sidebar);
        }

        editor = new mxEditor();
        editor.setGraphContainer(container);

        // gerenciamento de conexão global
        editor.graph.setConnectable(true);
        editor.graph.setCellsDisconnectable(true);
        editor.graph.setPanning(true);
        editor.graph.setAllowDanglingEdges(false);

        // altera o selecionado em um painel separado
        editor.graph.getSelectionModel().addListener(mxEvent.CHANGE, () => {
          this.selectionChanged();
        });
        this.selectionChanged();

        editor.graph.centerZoom = false;
        editor.graph.swimlaneNesting = false;
        editor.graph.dropEnabled = true;

        //Os campos são rótulos HTML criados dinamicamente
        editor.graph.isHtmlLabel = function (cell) {
          return !this.isSwimlane(cell) && !this.model.isEdge(cell);
        };

        // não editável
        editor.graph.isCellEditable = function () {
          return false;
          // certo é = true
        };

        // Retorna a propriedade do nome do objeto de usuário para o rótulo
        editor.graph.convertValueToString = function (cell) {
          if (cell.value != null && cell.value.name != null) {
            return cell.value.name;
          }
          return mxGraph.prototype.convertValueToString.apply(this, arguments); // "supercall"
        };

        // Cria um rótulo HTML dinâmico para propriedades
        editor.graph.getLabel = function (cell) {
          // console.log('getLabel ', cell);
          if (cell && this.isHtmlLabel(cell) && cell.value) {
            // TODO aqui está o código html para o conteúdo do campo
            let label = "";
            label +=
              '<div style="width: 100%; display: flex; justify-content: space-between; align-items: center"; >';
            label +=
              '<div style="width: 100px; margin-left: 10px; font-weight: 600"; >' +
              "<strong>" +
              mxUtils.htmlEntities(cell.value.name, false) +
              "</strong>" +
              "</div>";

            label += "</div>";

            return label;
          }

          return mxGraph.prototype.getLabel.apply(this, arguments); // "supercall"
        };

        // Adiciona o ícone da barra lateral para o objeto de propriedade
        let customObject = new window.Empresa();

        let object = new mxCell(
          customObject,
          new mxGeometry(0, 0, 200, 50),
          ""
          // new image para passar a imagem ao invés de um objeto retangular
        );
        object.setVertex(true);
        object.setConnectable(true);

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