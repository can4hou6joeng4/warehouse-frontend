<template>
  <div>
    <el-table :data="contractList" ref="multipleTableRef" @selection-change="handleSelectionChange"
              style="width: 100%; margin-top: 10px;" border stripe id="elTable" v-if="!showEngineering">
      <el-table-column prop="contractId" label="合同ID"/>
      <el-table-column prop="contractName" label="合同名" width="120"/>
      <el-table-column prop="productName" label="产品名称" width="120"/>
      <el-table-column prop="productNum" label="生产数量" width="120"/>
      <el-table-column prop="startTime" label="工期开始时间" width="120"/>
      <el-table-column prop="endTime" label="工期结束时间" width="120"/>
      <el-table-column prop="workRegion" label="关联工区" width="120"/>
      <el-table-column prop="custom" label="合同客户" width="120"/>
      <el-table-column label="合同状态" width="120">
        <template #default="props"> 
        <span :class="{red:props.row.contractState ==='0' || props.row.contractState ==='1'}">
          {{
            props.row.contractState === '0' ? '未审核'
                : props.row.contractState === '1' ? '被驳回'
                    : props.row.contractState === '2' ? '待结算'
                        : props.row.contractState === '3' ? '结算中'
                            : props.row.contractState === '4' ? '已结算'
                                : '其他'
          }}
        </span>
        </template>
      </el-table-column>
      <el-table-column label="是否需要采购" width="120">
        <template #default="props">
        <span :class="{red:props.row.ifPurchase=='1'}">{{
            props.row.ifPurchase == "1" ? "需要采购" : "无需采购"
          }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="createTime" label="创建时间" width="100"/>
      <el-table-column label="操作" fixed="right" width="240">
        <template #default="props">
<!--                  v-if="props.row.contractState ===   '0' || props.row.contractState === '1'"-->
          <el-link type="primary"
                   v-if="props.row.contractState === '0' && props.row.contractId != '' && showExamine==true"
                   @click.prevent="openContractUpdate(props.row)" style="margin-right: 8px">修改
          </el-link>
          <el-link type="primary" @click="openContractDetail(props.row)" style="margin-right: 8px">查看合同详情</el-link>
          <el-link type="primary" @click="downloadFiles(props.row)" style="margin-right: 8px">下载附件</el-link>
          <el-link type="primary" @click="agree(props.row)"
                   v-if="props.row.contractState === '0' && props.row.contractId != '' && showExamine==true"
                   style="margin-right: 8px">通过
          </el-link>
          <el-link type="primary" @click="reject(props.row.contractId,props.row.ifPurchase)"
                   v-if="props.row.contractState === '0' && props.row.contractId != ''  && showExamine==true"
                   style="margin-right: 8px">退回
          </el-link>
          <el-link type="primary" v-if="props.row.contractState === '1'" @click.prevent="completeTask(props.row)"
                   style="margin-right: 8px">
            再次提交审核
          </el-link>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script setup>
import {onMounted, ref} from "vue";
import {get} from "@/common";

const props = defineProps({
  paramsData: {
    type: Object,
    required: true,
  },
});


const c = ref("{\n" +
    "    \"tables_result\": [\n" +
    "        {\n" +
    "            \"header\": [],\n" +
    "            \"body\": [\n" +
    "                {\n" +
    "                    \"col_end\": 1,\n" +
    "                    \"row_end\": 1,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 309,\n" +
    "                            \"y\": 629\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 545,\n" +
    "                            \"y\": 631\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 544,\n" +
    "                            \"y\": 782\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 308,\n" +
    "                            \"y\": 781\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 0,\n" +
    "                    \"col_start\": 0,\n" +
    "                    \"words\": \"序号\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 2,\n" +
    "                    \"row_end\": 1,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 545,\n" +
    "                            \"y\": 631\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 878,\n" +
    "                            \"y\": 633\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 877,\n" +
    "                            \"y\": 784\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 544,\n" +
    "                            \"y\": 782\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 0,\n" +
    "                    \"col_start\": 1,\n" +
    "                    \"words\": \"结构层\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 3,\n" +
    "                    \"row_end\": 1,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 878,\n" +
    "                            \"y\": 633\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1117,\n" +
    "                            \"y\": 635\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1115,\n" +
    "                            \"y\": 786\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 877,\n" +
    "                            \"y\": 784\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 0,\n" +
    "                    \"col_start\": 2,\n" +
    "                    \"words\": \"规格\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 4,\n" +
    "                    \"row_end\": 1,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1117,\n" +
    "                            \"y\": 635\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1353,\n" +
    "                            \"y\": 637\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1352,\n" +
    "                            \"y\": 787\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1115,\n" +
    "                            \"y\": 786\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 0,\n" +
    "                    \"col_start\": 3,\n" +
    "                    \"words\": \"单位\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 5,\n" +
    "                    \"row_end\": 1,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1353,\n" +
    "                            \"y\": 637\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1592,\n" +
    "                            \"y\": 639\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1590,\n" +
    "                            \"y\": 789\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1352,\n" +
    "                            \"y\": 787\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 0,\n" +
    "                    \"col_start\": 4,\n" +
    "                    \"words\": \"暂定\\n数量\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 6,\n" +
    "                    \"row_end\": 1,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1592,\n" +
    "                            \"y\": 639\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1842,\n" +
    "                            \"y\": 640\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1840,\n" +
    "                            \"y\": 790\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1590,\n" +
    "                            \"y\": 789\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 0,\n" +
    "                    \"col_start\": 5,\n" +
    "                    \"words\": \"单价\\n(元)\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 7,\n" +
    "                    \"row_end\": 1,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1842,\n" +
    "                            \"y\": 640\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2138,\n" +
    "                            \"y\": 643\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2136,\n" +
    "                            \"y\": 792\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1840,\n" +
    "                            \"y\": 790\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 0,\n" +
    "                    \"col_start\": 6,\n" +
    "                    \"words\": \"总价（元）\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 8,\n" +
    "                    \"row_end\": 1,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 2138,\n" +
    "                            \"y\": 643\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2372,\n" +
    "                            \"y\": 644\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2370,\n" +
    "                            \"y\": 794\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2136,\n" +
    "                            \"y\": 792\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 0,\n" +
    "                    \"col_start\": 7,\n" +
    "                    \"words\": \"备注\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 1,\n" +
    "                    \"row_end\": 2,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 308,\n" +
    "                            \"y\": 781\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 544,\n" +
    "                            \"y\": 782\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 541,\n" +
    "                            \"y\": 1079\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 305,\n" +
    "                            \"y\": 1078\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 1,\n" +
    "                    \"col_start\": 0,\n" +
    "                    \"words\": \"1\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 2,\n" +
    "                    \"row_end\": 2,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 544,\n" +
    "                            \"y\": 782\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 877,\n" +
    "                            \"y\": 784\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 874,\n" +
    "                            \"y\": 1081\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 541,\n" +
    "                            \"y\": 1079\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 1,\n" +
    "                    \"col_start\": 1,\n" +
    "                    \"words\": \"细粒式\\nAC-13沥青\\n混凝土上面\\n层\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 3,\n" +
    "                    \"row_end\": 2,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 877,\n" +
    "                            \"y\": 784\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1115,\n" +
    "                            \"y\": 786\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1113,\n" +
    "                            \"y\": 1083\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 874,\n" +
    "                            \"y\": 1081\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 1,\n" +
    "                    \"col_start\": 2,\n" +
    "                    \"words\": \"厚度\\n4cm\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 4,\n" +
    "                    \"row_end\": 3,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1115,\n" +
    "                            \"y\": 786\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1352,\n" +
    "                            \"y\": 787\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1345,\n" +
    "                            \"y\": 1453\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1109,\n" +
    "                            \"y\": 1451\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 1,\n" +
    "                    \"col_start\": 3,\n" +
    "                    \"words\": \"M2\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 5,\n" +
    "                    \"row_end\": 3,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1352,\n" +
    "                            \"y\": 787\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1589,\n" +
    "                            \"y\": 789\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1583,\n" +
    "                            \"y\": 1455\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1345,\n" +
    "                            \"y\": 1453\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 1,\n" +
    "                    \"col_start\": 4,\n" +
    "                    \"words\": \"525\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 6,\n" +
    "                    \"row_end\": 3,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1589,\n" +
    "                            \"y\": 789\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1839,\n" +
    "                            \"y\": 790\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1831,\n" +
    "                            \"y\": 1457\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1583,\n" +
    "                            \"y\": 1455\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 1,\n" +
    "                    \"col_start\": 5,\n" +
    "                    \"words\": \"92.5\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 7,\n" +
    "                    \"row_end\": 3,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1839,\n" +
    "                            \"y\": 790\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2136,\n" +
    "                            \"y\": 792\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2129,\n" +
    "                            \"y\": 1459\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1831,\n" +
    "                            \"y\": 1457\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 1,\n" +
    "                    \"col_start\": 6,\n" +
    "                    \"words\": \"48562.5\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 8,\n" +
    "                    \"row_end\": 2,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 2136,\n" +
    "                            \"y\": 792\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2370,\n" +
    "                            \"y\": 794\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2367,\n" +
    "                            \"y\": 1092\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2133,\n" +
    "                            \"y\": 1090\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 1,\n" +
    "                    \"col_start\": 7,\n" +
    "                    \"words\": \"此单价\\n为含13%\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 1,\n" +
    "                    \"row_end\": 3,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 305,\n" +
    "                            \"y\": 1078\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 541,\n" +
    "                            \"y\": 1079\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 537,\n" +
    "                            \"y\": 1448\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 301,\n" +
    "                            \"y\": 1446\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 2,\n" +
    "                    \"col_start\": 0,\n" +
    "                    \"words\": \"2\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 2,\n" +
    "                    \"row_end\": 3,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 541,\n" +
    "                            \"y\": 1079\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 874,\n" +
    "                            \"y\": 1081\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 870,\n" +
    "                            \"y\": 1450\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 537,\n" +
    "                            \"y\": 1448\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 2,\n" +
    "                    \"col_start\": 1,\n" +
    "                    \"words\": \"乳化沥青粘\\n层油\\n(PC-3)、\\n铣刨\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 3,\n" +
    "                    \"row_end\": 3,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 874,\n" +
    "                            \"y\": 1081\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1113,\n" +
    "                            \"y\": 1083\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1109,\n" +
    "                            \"y\": 1452\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 870,\n" +
    "                            \"y\": 1450\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 2,\n" +
    "                    \"col_start\": 2,\n" +
    "                    \"words\": \"\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 8,\n" +
    "                    \"row_end\": 3,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 2133,\n" +
    "                            \"y\": 1090\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2367,\n" +
    "                            \"y\": 1092\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2363,\n" +
    "                            \"y\": 1461\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2129,\n" +
    "                            \"y\": 1460\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 2,\n" +
    "                    \"col_start\": 7,\n" +
    "                    \"words\": \"增值税\\n专用发\\n票价格\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 1,\n" +
    "                    \"row_end\": 4,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 301,\n" +
    "                            \"y\": 1446\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 537,\n" +
    "                            \"y\": 1448\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 534,\n" +
    "                            \"y\": 1781\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 298,\n" +
    "                            \"y\": 1779\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 3,\n" +
    "                    \"col_start\": 0,\n" +
    "                    \"words\": \"3\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 2,\n" +
    "                    \"row_end\": 4,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 537,\n" +
    "                            \"y\": 1448\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 870,\n" +
    "                            \"y\": 1450\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 867,\n" +
    "                            \"y\": 1783\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 534,\n" +
    "                            \"y\": 1781\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 3,\n" +
    "                    \"col_start\": 1,\n" +
    "                    \"words\": \"细粒式\\nAC-13沥青\\n混凝土上面\\n层\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 3,\n" +
    "                    \"row_end\": 4,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 870,\n" +
    "                            \"y\": 1450\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1109,\n" +
    "                            \"y\": 1452\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1106,\n" +
    "                            \"y\": 1784\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 867,\n" +
    "                            \"y\": 1783\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 3,\n" +
    "                    \"col_start\": 2,\n" +
    "                    \"words\": \"厚度\\n3cm\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 4,\n" +
    "                    \"row_end\": 6,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1109,\n" +
    "                            \"y\": 1451\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1345,\n" +
    "                            \"y\": 1453\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1338,\n" +
    "                            \"y\": 2232\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1102,\n" +
    "                            \"y\": 2231\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 3,\n" +
    "                    \"col_start\": 3,\n" +
    "                    \"words\": \"M2\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 5,\n" +
    "                    \"row_end\": 5,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1345,\n" +
    "                            \"y\": 1453\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1583,\n" +
    "                            \"y\": 1455\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1575,\n" +
    "                            \"y\": 2157\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1338,\n" +
    "                            \"y\": 2155\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 3,\n" +
    "                    \"col_start\": 4,\n" +
    "                    \"words\": \"526\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 6,\n" +
    "                    \"row_end\": 5,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1583,\n" +
    "                            \"y\": 1455\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1831,\n" +
    "                            \"y\": 1457\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1824,\n" +
    "                            \"y\": 2158\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1575,\n" +
    "                            \"y\": 2157\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 3,\n" +
    "                    \"col_start\": 5,\n" +
    "                    \"words\": \"全包\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 7,\n" +
    "                    \"row_end\": 5,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1831,\n" +
    "                            \"y\": 1457\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2129,\n" +
    "                            \"y\": 1459\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2122,\n" +
    "                            \"y\": 2160\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1824,\n" +
    "                            \"y\": 2158\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 3,\n" +
    "                    \"col_start\": 6,\n" +
    "                    \"words\": \"59664\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 8,\n" +
    "                    \"row_end\": 5,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 2129,\n" +
    "                            \"y\": 1459\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2363,\n" +
    "                            \"y\": 1461\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2356,\n" +
    "                            \"y\": 2162\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2122,\n" +
    "                            \"y\": 2160\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 3,\n" +
    "                    \"col_start\": 7,\n" +
    "                    \"words\": \"此单价\\n为含13%\\n增值税\\n专用发\\n票价格\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 1,\n" +
    "                    \"row_end\": 5,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 298,\n" +
    "                            \"y\": 1779\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 534,\n" +
    "                            \"y\": 1781\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 531,\n" +
    "                            \"y\": 2150\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 295,\n" +
    "                            \"y\": 2148\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 4,\n" +
    "                    \"col_start\": 0,\n" +
    "                    \"words\": \"4\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 2,\n" +
    "                    \"row_end\": 5,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 534,\n" +
    "                            \"y\": 1781\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 867,\n" +
    "                            \"y\": 1783\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 864,\n" +
    "                            \"y\": 2152\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 531,\n" +
    "                            \"y\": 2150\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 4,\n" +
    "                    \"col_start\": 1,\n" +
    "                    \"words\": \"乳化沥青粘\\n层油\\n(PC-3\\n铣\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 3,\n" +
    "                    \"row_end\": 5,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 867,\n" +
    "                            \"y\": 1783\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1106,\n" +
    "                            \"y\": 1784\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1103,\n" +
    "                            \"y\": 2154\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 864,\n" +
    "                            \"y\": 2152\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 4,\n" +
    "                    \"col_start\": 2,\n" +
    "                    \"words\": \"\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 1,\n" +
    "                    \"row_end\": 6,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 295,\n" +
    "                            \"y\": 2148\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 531,\n" +
    "                            \"y\": 2150\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 530,\n" +
    "                            \"y\": 2228\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 294,\n" +
    "                            \"y\": 2226\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 5,\n" +
    "                    \"col_start\": 0,\n" +
    "                    \"words\": \"\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 6,\n" +
    "                    \"row_end\": 6,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 530,\n" +
    "                            \"y\": 2149\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1824,\n" +
    "                            \"y\": 2158\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1823,\n" +
    "                            \"y\": 2236\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 529,\n" +
    "                            \"y\": 2227\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 5,\n" +
    "                    \"col_start\": 1,\n" +
    "                    \"words\": \"\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 7,\n" +
    "                    \"row_end\": 6,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1824,\n" +
    "                            \"y\": 2159\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2122,\n" +
    "                            \"y\": 2161\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2121,\n" +
    "                            \"y\": 2238\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1823,\n" +
    "                            \"y\": 2236\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 5,\n" +
    "                    \"col_start\": 6,\n" +
    "                    \"words\": \"\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 8,\n" +
    "                    \"row_end\": 6,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 2122,\n" +
    "                            \"y\": 2161\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2356,\n" +
    "                            \"y\": 2163\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2355,\n" +
    "                            \"y\": 2240\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2121,\n" +
    "                            \"y\": 2238\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 5,\n" +
    "                    \"col_start\": 7,\n" +
    "                    \"words\": \"\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 1,\n" +
    "                    \"row_end\": 7,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 294,\n" +
    "                            \"y\": 2226\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 530,\n" +
    "                            \"y\": 2228\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 529,\n" +
    "                            \"y\": 2303\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 294,\n" +
    "                            \"y\": 2302\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 6,\n" +
    "                    \"col_start\": 0,\n" +
    "                    \"words\": \"\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 6,\n" +
    "                    \"row_end\": 7,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 529,\n" +
    "                            \"y\": 2227\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1823,\n" +
    "                            \"y\": 2236\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1821,\n" +
    "                            \"y\": 2312\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 529,\n" +
    "                            \"y\": 2303\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 6,\n" +
    "                    \"col_start\": 1,\n" +
    "                    \"words\": \"合计（元）\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 7,\n" +
    "                    \"row_end\": 7,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 1823,\n" +
    "                            \"y\": 2236\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2121,\n" +
    "                            \"y\": 2238\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2120,\n" +
    "                            \"y\": 2314\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1822,\n" +
    "                            \"y\": 2312\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 6,\n" +
    "                    \"col_start\": 6,\n" +
    "                    \"words\": \"108226.5\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"col_end\": 8,\n" +
    "                    \"row_end\": 7,\n" +
    "                    \"cell_location\": [\n" +
    "                        {\n" +
    "                            \"x\": 2121,\n" +
    "                            \"y\": 2238\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2355,\n" +
    "                            \"y\": 2240\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2354,\n" +
    "                            \"y\": 2316\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2120,\n" +
    "                            \"y\": 2314\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"row_start\": 6,\n" +
    "                    \"col_start\": 7,\n" +
    "                    \"words\": \"\"\n" +
    "                }\n" +
    "            ],\n" +
    "            \"table_location\": [\n" +
    "                {\n" +
    "                    \"x\": 294,\n" +
    "                    \"y\": 629\n" +
    "                },\n" +
    "                {\n" +
    "                    \"x\": 2372,\n" +
    "                    \"y\": 629\n" +
    "                },\n" +
    "                {\n" +
    "                    \"x\": 2372,\n" +
    "                    \"y\": 2316\n" +
    "                },\n" +
    "                {\n" +
    "                    \"x\": 294,\n" +
    "                    \"y\": 2316\n" +
    "                }\n" +
    "            ],\n" +
    "            \"footer\": [\n" +
    "                {\n" +
    "                    \"location\": [\n" +
    "                        {\n" +
    "                            \"x\": 289,\n" +
    "                            \"y\": 2635\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2494,\n" +
    "                            \"y\": 2649\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 2494,\n" +
    "                            \"y\": 2719\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 289,\n" +
    "                            \"y\": 2705\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"words\": \"备注：1，以上所述数量为暂定数量，结算数量以甲方确认工程量为结算依据。\"\n" +
    "                },\n" +
    "                {\n" +
    "                    \"location\": [\n" +
    "                        {\n" +
    "                            \"x\": 289,\n" +
    "                            \"y\": 2781\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1667,\n" +
    "                            \"y\": 2790\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 1667,\n" +
    "                            \"y\": 2860\n" +
    "                        },\n" +
    "                        {\n" +
    "                            \"x\": 289,\n" +
    "                            \"y\": 2851\n" +
    "                        }\n" +
    "                    ],\n" +
    "                    \"words\": \"以上所述单价为含税价，开具13%的增值税发票。\"\n" +
    "                }\n" +
    "            ]\n" +
    "        }\n" +
    "    ],\n" +
    "    \"table_num\": 1,\n" +
    "    \"log_id\": \"1742731110691686999\"\n" +
    "}")

onMounted(hook => {
  // console.log(props.paramsData)
  
  let json = JSON.parse(c.value).tables_result[0].body;
  console.log(json)
  
  // 先获取每一行的列数
  let col = 1; // 列数
  for(let i =1;i<json[0].length;i++){
    if(json[i].col_end==1){
      break;
    }
    col++;
  }
  console.log(col)

  // let row = json[0].body[json[0].body.length-1].row_end // 列数
  // console.log(row)
  //
  
  let tableList = []
  // 遍历数据，补全缺失列
  for (let i = 1; i < json.length; i++) {
    let prevCol = json[i - 1]; // 上一列数据
    let currentCol = json[i]; // 当前列数据
    let prevColEnd = prevCol.col_end; // 上一列位置
    let currentColStart = currentCol.col_end; // 当前列位置
    let row = json[i].row_start // 当前行位置
    // console.log(row)
    // 计算缺失的列数
    let missingCols = currentColStart - prevColEnd -1;
    console.log("当前列的位置："+currentColStart)
    if(missingCols>0){
      console.log("当前位置",currentColStart)
      if(currentColStart != col){
        for (let i = 1;i < col-currentColStart;i++){
          console.log(json.find(item => item.col_end === currentColStart+i && item.row_start === row-1))
          if (json.find(item => item.col_end === currentColStart+i && item.row_start === row-1).words != undefined){
            tableList.push(json.find(item => item.col_end === currentColStart+i && item.row_start === row-1).words)
          }
        }
      }else{
        for (let i = 1;i < missingCols+1;i++){
          console.log(currentColStart-i)
          console.log(json.find(item => item.col_end === currentColStart-i && item.row_start === row-1))
          if (json.find(item => item.col_end === currentColStart-i && item.row_start === row-1).words != undefined){
            tableList.push(json.find(item => item.col_end === currentColStart-i && item.row_start === row-1).words)
          }
        }
      }
    }else{
      tableList.push(json[i].words)
    }
    console.log("当前列的位置与上一个列的差值:"+missingCols)
  }
  console.log(tableList)
  // console.log(json)
  // for(let i =0;i<json[0].body.length;i++){
  //   console.log(json[0].body[i].col_end)
  // }
  //
  
  // let tableContent = {}
  // if (j>5){
  //   tableContent = {id:'',productName:'',specs:'',unit:'',price:'',total:"",remarks:''}
  // }else{
  //   tableContent = {id:'',productName:'',specs:'',unit:'',price:'',remarks:''}
  // }
  //
  // const keys = Object.keys(tableContent);
  //
  // for(let i =0;i<json[0].body.length/j;i++){
  //   tableHeader.value = tableContent
  //
  //   for (let index=0;index<=json[0].body.length/j;index++){
  //     tableContent[keys[index]] = json[0].body[i*6+index].words
  //   }
  //   tableList.value.push(Object.assign({}, tableContent))
  // }
})
</script>

<style scoped>

</style>