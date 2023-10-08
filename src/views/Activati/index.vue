<template>
  <div class="containerBox" style="position: relative;">
    <div id="container" style="width: calc(100vw - 750px); height: calc(100vh - 150px)">
    </div>
    <div id="js-properties-panel" class="panel"></div>
  </div>
</template>
<script setup>
import { onMounted, markRaw } from 'vue';

// bpmn-js相关
import 'bpmn-js/dist/assets/diagram-js.css'; // 左边工具栏以及编辑节点的样式
import 'bpmn-js/dist/assets/bpmn-font/css/bpmn-embedded.css';
import BpmnModeler from 'bpmn-js/lib/Modeler';

// bpmn-js-properties-panel相关

import 'bpmn-js-properties-panel/dist/assets/bpmn-js-properties-panel.css'

import propertiesPanelModule from 'bpmn-js-properties-panel'

import propertiesProviderModule from 'bpmn-js-properties-panel/lib/provider/camunda'

import camundaModdleDescriptor from 'camunda-bpmn-moddle/resources/camunda'
import translate from '@/common/bpmn/translate'

onMounted(() => {
  const containerEl = document.getElementById('container');


  var customTranslateModule = {
    translate: ['value', translate]
  }

  const bpmnModeler = markRaw(new BpmnModeler({
    container: containerEl,
    // 添加控制板
    propertiesPanel: {
      parent: '#js-properties-panel'
    },
    // 右侧属性面板
    additionalModules: [
      propertiesPanelModule,
      propertiesProviderModule,
      customTranslateModule
    ],
    moddleExtensions: {
      camunda: camundaModdleDescriptor
    }
  }));
  bpmnModeler.createDiagram(() => {
    bpmnModeler.get('canvas').zoom('fit-viewport');
  });
})


</script>
<style>
.containerBox {
  height: calc(100vh - 160px);
  margin-top: 30px;
}

.containerBox #container {
  height: calc(100vh - 160px);
  border: 1px solid rgb(121, 121, 121);
}

.bpp-properties-panel [type=text] {
  box-sizing: border-box;
}

.panel {
  width: 400px;
  position: absolute;
  top: 1px;
  right: 1px;
  height: 100%;
  overflow: auto;
}

/* 右下角logo */
.bjs-powered-by {
  display: none;
}
</style>
