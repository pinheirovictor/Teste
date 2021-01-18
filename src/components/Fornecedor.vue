<template>
  <div>
    <div id="container"></div>
    <div id="sidebar">
      <EditForm
        ref="form"
        @change="changeObjectValues"
        :cell-data="currentCell"
      />
    </div>
  </div>
</template>

<script>
import mxgraph from "mxgraph";
import EditForm from "./EditForm";
import Empresa from "./Empresa";

// если планируется использовать встроенные интерфейсы то нужно указать пути к ресурсам
const graphConfig = {
  mxBasePath: "/mx/", //Specifies the path in mxClient.basePath.
  mxImageBasePath: "/mx/images", // Specifies the path in mxClient.imageBasePath.
  mxLanguage: "en", // Specifies the language for resources in mxClient.language.
  mxDefaultLanguage: "en", // Specifies the default language in mxClient.defaultLanguage.
  mxLoadResources: false, // Specifies if any resources should be loaded.  Default is true.
  mxLoadStylesheets: false, // Specifies if any stylesheets should be loaded.  Default is true
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
window.CustomUserObject = function (name) {
  this.name = name = "tipo";
  this.clone = function () {
    return mxUtils.clone(this);
  };
};

export default {
  name: "Fornecedor",
  components: { EditForm },
  data() {
    return {
      currentCell: null,
    };
  },

  methods: {
    // altera o selecionado em um painel separado
    // transferir o objeto para o ambiente VUE
    selectionChanged() {
      let cell = editor.graph.getSelectionCell();
      this.$set(this, "currentCell", cell);
    },

    // retorna o valor atualizado para o ambiente mxGraph
    changeObjectValues(newCellValue) {
      editor.graph.model.setValue(this.currentCell, newCellValue);
    },

    // adiciona os elementos na menu lateral
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

      // Creates the image which is used as the sidebar icon (drag source) - Agregador
      let wrapper6 = document.createElement("div");
      wrapper6.style.cursor = "pointer";
      wrapper6.style.backgroundColor = "red";
      wrapper6.style.webkitTransform = "skew(20deg)";
      wrapper6.style.margin = "10px";
      wrapper6.style.width = "200px";
      wrapper6.style.height = "60px";
      wrapper6.style.textAlign = "center";
      wrapper6.style.display = "flex";
      wrapper6.style.flexWrap = "wrap";
      wrapper6.style.alignItems = "center";
      wrapper6.style.justifyContent = "center";
      wrapper6.innerHTML =
        '<div style="color: #00000; -webkit-transform: skew(-20deg); -moz-transform: skew(-20deg); -o-transform: skew(-20deg); "><strong>Agregador</strong></div>';
      sidebar.appendChild(wrapper6);

      // Creates the image which is used as the drag icon (preview)
      let dragImage6 = wrapper6.cloneNode(true);
      mxUtils.makeDraggable(wrapper6, graph, funct, dragImage6);

      // Creates the image which is used as the sidebar icon (drag source) - Cliente do cliente
      let wrapper5 = document.createElement("div");
      wrapper5.style.cursor = "pointer";
      wrapper5.style.backgroundColor = "gray";
      wrapper5.style.margin = "20px";
      wrapper5.style.width = "200px";
      wrapper5.style.height = "60px";
      wrapper5.style.textAlign = "center";
      wrapper5.style.display = "flex";
      wrapper5.style.flexWrap = "wrap";
      wrapper5.style.alignItems = "center";
      wrapper5.style.justifyContent = "center";
      wrapper5.innerHTML =
        '<div style="color: #00000"><strong>Cliente do Cliente</strong></div>';
      sidebar.appendChild(wrapper5);

      // Creates the image which is used as the drag icon (preview)
      let dragImage5 = wrapper5.cloneNode(true);
      mxUtils.makeDraggable(wrapper5, graph, funct, dragImage5);

      // Creates the image which is used as the sidebar icon (drag source) - Intermediário
      let wrapper4 = document.createElement("div");
      wrapper4.style.cursor = "pointer";
      wrapper4.style.backgroundColor = "green";
      wrapper4.style.margin = "10px";
      wrapper4.style.width = "200px";
      wrapper4.style.height = "60px";
      wrapper4.style.textAlign = "center";
      wrapper4.style.display = "flex";
      wrapper4.style.flexWrap = "wrap";
      wrapper4.style.alignItems = "center";
      wrapper4.style.justifyContent = "center";
      //wrapper4.style.position = "relative";
      wrapper4.innerHTML =
        '<div style="color: #00000"><strong>Intermediário</strong></div>';
      sidebar.appendChild(wrapper4);

      // Creates the image which is used as the drag icon (preview)
      let dragImage4 = wrapper4.cloneNode(true);
      mxUtils.makeDraggable(wrapper4, graph, funct, dragImage4);

      // Creates the image which is used as the sidebar icon (drag source) - cliente
      let wrapper3 = document.createElement("div");
      wrapper3.style.cursor = "pointer";
      wrapper3.style.backgroundColor = "yellow";
      wrapper3.style.margin = "10px";
      wrapper3.style.width = "200px";
      wrapper3.style.height = "60px";
      wrapper3.style.textAlign = "center";
      wrapper3.style.display = "flex";
      wrapper3.style.flexWrap = "wrap";
      wrapper3.style.alignItems = "center";
      wrapper3.style.justifyContent = "center";
      wrapper3.innerHTML =
        '<div style="color: #00000"><strong>Cliente</strong></div>';
      sidebar.appendChild(wrapper3);

      // Creates the image which is used as the drag icon (preview)
      let dragImage3 = wrapper3.cloneNode(true);
      mxUtils.makeDraggable(wrapper3, graph, funct, dragImage3);

      // Creates the image which is used as the sidebar icon (drag source) - fornecedor
      let wrapper2 = document.createElement("div");
      wrapper2.style.cursor = "pointer";
      wrapper2.style.backgroundColor = "#FFA500";
      wrapper2.style.margin = "10px";
      wrapper2.style.width = "200px";
      wrapper2.style.height = "60px";
      wrapper2.style.textAlign = "center";
      wrapper2.style.display = "flex";
      wrapper2.style.flexWrap = "wrap";
      wrapper2.style.alignItems = "center";
      wrapper2.style.justifyContent = "center";
      wrapper2.innerHTML =
        '<div style="color: #00000"><strong>Fornecedor</strong></div>';
      sidebar.appendChild(wrapper2);

      // Creates the image which is used as the drag icon (preview)
      let dragImage2 = wrapper2.cloneNode(true);
      mxUtils.makeDraggable(wrapper2, graph, funct, dragImage2);

      // // Creates the image which is used as the sidebar icon (drag source) - empresa de interesse
      // let wrapper = document.createElement("div");
      // wrapper.style.cursor = "pointer";
      // wrapper.style.backgroundColor = "blue";
      // //wrapper.style.top = "200px";
      // wrapper.style.margin = "10px";
      // wrapper.style.width = "200px";
      // wrapper.style.height = "60px";
      // wrapper.style.textAlign = "center";
      // wrapper.style.display = "flex";
      // wrapper.style.flexWrap = "wrap";
      // wrapper.style.alignItems = "center";
      // wrapper.style.justifyContent = "center";
      // wrapper.innerHTML =
      //   '<div style="color: #F8F8FF"><strong>Empresa de Interesse</strong></div>';
      // sidebar.appendChild(wrapper);

      // // Creates the image which is used as the drag icon (preview)
      // let dragImage = wrapper.cloneNode(true);
      // mxUtils.makeDraggable(wrapper, graph, funct, dragImage);
    },

    // cria e configura o editor
    createGraph() {
      // Verifica se o navegador é compatível
      if (!mxClient.isBrowserSupported()) {
        // Exibe uma mensagem de erro se o navegador não for compatível.
        mxUtils.error("Browser is not supported!", 200, false);
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

        // глобальное управление подключениями
        editor.graph.setConnectable(true);
        editor.graph.setCellsDisconnectable(true);
        editor.graph.setPanning(true);
        editor.graph.setAllowDanglingEdges(false);

        // изменение выбранных в отдельной панели
        editor.graph.getSelectionModel().addListener(mxEvent.CHANGE, () => {
          this.selectionChanged();
        });
        this.selectionChanged();

        editor.graph.centerZoom = false;
        editor.graph.swimlaneNesting = false;
        editor.graph.dropEnabled = true;

        // Fields are dynamically created HTML labels
        editor.graph.isHtmlLabel = function (cell) {
          return !this.isSwimlane(cell) && !this.model.isEdge(cell);
        };

        // not editable
        editor.graph.isCellEditable = function () {
          return false;
          // certo = true
        };

        // Returns the name propertie of the user object for the label
        editor.graph.convertValueToString = function (cell) {
          if (cell.value != null && cell.value.name != null) {
            return cell.value.name;
          }
          return mxGraph.prototype.convertValueToString.apply(this, arguments); // "supercall"
        };

        // Creates a dynamic HTML label for properties
        editor.graph.getLabel = function (cell) {
          // console.log('getLabel ', cell);
          if (cell && this.isHtmlLabel(cell) && cell.value) {
            // TODO тут код html для содержимого Field
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

        // Adds sidebar icon for the propertie object
        let customObject = new window.CustomUserObject();

        let object = new mxCell(
          customObject,
          new mxGeometry(0, 0, 200, 50),
          ""
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
      codecCustomUserObject.encode = function (enc, obj) {
        let node = enc.document.createElement("Nome");
        mxUtils.setTextContent(node, JSON.stringify(obj));
        console.log(node);
        return node;
      };
      codecCustomUserObject.decode = function (dec, node) {
        let obj = JSON.parse(mxUtils.getTextContent(node));
        let beatyObj = new window.CustomUserObject();
        obj = Object.assign(beatyObj, obj);
        return obj;
      };
      mxCodecRegistry.register(codecCustomUserObject);

      this.createGraph();
    },
  },
  mounted() {
    this.init();
  },
};
</script>
