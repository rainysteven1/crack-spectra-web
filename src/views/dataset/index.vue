<script setup lang="ts">
import { h, ref } from 'vue';
import { NButton, NDataTable, NIcon, NInput, NPagination, NSpace, NTabPane, NTabs, NTag } from 'naive-ui';
import { Road } from '@vicons/fa';
import { AddOutline, CloudUploadOutline, SearchOutline } from '@vicons/ionicons5';

// 标签页相关
const tabValue = ref('all');

type StatusType = '已发布' | '标注中' | '待审核';
type NTagType = 'success' | 'warning' | 'error' | 'default' | 'primary' | 'info';

interface DatasetRow {
  id: number;
  name: string;
  description: string;
  type: string;
  version: string;
  versionCount: number;
  status: StatusType;
  samples: number;
  updateTime: string;
}

const statusMap: Record<StatusType, { type: NTagType }> = {
  已发布: { type: 'success' },
  标注中: { type: 'warning' },
  待审核: { type: 'error' }
};

// 表格数据
const datasets = ref([
  {
    id: 1,
    name: '城市道路裂缝数据集',
    description: '用于道路裂缝检测与分割',
    type: '图像分割',
    version: 'v2.1',
    versionCount: 3,
    status: '已发布',
    samples: 12450,
    updateTime: '2023-06-15'
  },
  {
    id: 2,
    name: '高速公路裂缝数据集',
    description: '高速公路路面裂缝检测',
    type: '图像分割',
    version: 'v1.5',
    versionCount: 0,
    status: '标注中',
    samples: 8732,
    updateTime: '2023-05-28'
  },
  {
    id: 3,
    name: '桥梁裂缝数据集',
    description: '桥梁结构裂缝检测',
    type: '图像分割',
    version: 'v3.2',
    versionCount: 5,
    status: '已发布',
    samples: 15892,
    updateTime: '2023-07-02'
  },
  {
    id: 4,
    name: '隧道裂缝数据集',
    description: '隧道内壁裂缝检测',
    type: '图像分割',
    version: 'v0.8',
    versionCount: 0,
    status: '待审核',
    samples: 5321,
    updateTime: '2023-04-18'
  },
  {
    id: 5,
    name: '城市道路裂缝数据集',
    description: '用于道路裂缝检测与分割',
    type: '图像分割',
    version: 'v2.1',
    versionCount: 3,
    status: '已发布',
    samples: 12450,
    updateTime: '2023-06-15'
  },
  {
    id: 6,
    name: '高速公路裂缝数据集',
    description: '高速公路路面裂缝检测',
    type: '图像分割',
    version: 'v1.5',
    versionCount: 0,
    status: '标注中',
    samples: 8732,
    updateTime: '2023-05-28'
  },
  {
    id: 7,
    name: '桥梁裂缝数据集',
    description: '桥梁结构裂缝检测',
    type: '图像分割',
    version: 'v3.2',
    versionCount: 5,
    status: '已发布',
    samples: 15892,
    updateTime: '2023-07-02'
  },
  {
    id: 8,
    name: '隧道裂缝数据集',
    description: '隧道内壁裂缝检测',
    type: '图像分割',
    version: 'v0.8',
    versionCount: 0,
    status: '待审核',
    samples: 5321,
    updateTime: '2023-04-18'
  }
]);

// 分页相关
const page = ref(1);
const pageSize = ref(10);
const totalItems = ref(12);

// 表格列配置
const columns = [
  {
    title: '数据集名称',
    key: 'name',
    render(row: DatasetRow) {
      return h('div', { class: 'flex items-center' }, [
        h('div', { class: 'flex-shrink-0 mr-4' }, h(NIcon, { size: 24, color: '#3b82f6' }, { default: () => h(Road) })),
        h('div', [
          h('div', { class: 'font-medium' }, row.name),
          h('div', { class: 'text-sm text-gray-500' }, row.description)
        ])
      ]);
    }
  },
  {
    title: '类型',
    key: 'type',
    render(row: DatasetRow) {
      return h(NTag, { type: 'success', size: 'small' }, { default: () => row.type });
    }
  },
  {
    title: '版本',
    key: 'version',
    render(row: DatasetRow) {
      return h('div', { class: 'flex items-center' }, [
        // 外层容器使用相对定位
        h('div', { style: 'position: relative; display: inline-block;' }, [
          // NTag本身不变
          h(
            NTag,
            { type: 'info', size: 'small' },
            {
              default: () => row.version
            }
          ),

          // 徽章作为兄弟元素，而不是NTag的子元素
          row.versionCount > 0
            ? h('div', {
                style:
                  'position: absolute; top: -2px; right: -2px; background-color: #72F133; color: white; font-size: 10px; border-radius: 50%; width: 8px; height: 8px; display: flex; align-items: center; justify-content: center; z-index: 1;'
              })
            : null
        ]),

        // 版本数量文本
        row.versionCount > 0 ? h('span', { class: 'ml-2 text-xs text-gray-500' }, `+${row.versionCount}个版本`) : null
      ]);
    }
  },
  {
    title: '状态',
    key: 'status',
    render(row: DatasetRow) {
      return h(NTag, { type: statusMap[row.status].type, size: 'small' }, { default: () => row.status });
    }
  },
  {
    title: '样本数',
    key: 'samples'
  },
  {
    title: '更新时间',
    key: 'updateTime'
  },
  {
    title: '操作',
    key: 'actions',
    render(row: DatasetRow) {
      return h('div', [
        h(NButton, { text: true, type: 'primary', onClick: () => showVersionModal(row.id) }, { default: () => '版本' }),
        h(
          NButton,
          { text: true, type: 'primary', class: 'ml-3', onClick: () => editDataset(row.id) },
          { default: () => '编辑' }
        )
      ]);
    }
  }
];

// 处理函数
function showDatasetDetail(id: number) {
  console.log('查看数据集详情:', id);
}

function showVersionModal(id: number) {
  console.log('查看版本:', id);
}

function editDataset(id: number) {
  console.log('编辑数据集:', id);
}

function createDataset() {
  console.log('创建新数据集');
}

function batchImport() {
  console.log('批量导入');
}

function handlePageChange(currentPage: number) {
  page.value = currentPage;
}

// 行点击事件
const rowProps = (row: DatasetRow) => {
  return {
    style: 'cursor: pointer',
    onClick: () => showDatasetDetail(row.id)
  };
};
</script>

<template>
  <NSpace vertical :size="16">
    <!-- 数据集操作栏 -->
    <div class="flex items-center justify-between">
      <div class="flex space-x-3">
        <NButton type="primary" @click="createDataset">
          <template #icon>
            <NIcon><AddOutline /></NIcon>
          </template>
          新建数据集
        </NButton>
        <NButton @click="batchImport">
          <template #icon>
            <NIcon><CloudUploadOutline /></NIcon>
          </template>
          批量导入
        </NButton>
      </div>

      <NInput placeholder="搜索数据集..." style="width: 200px">
        <template #prefix>
          <NIcon><SearchOutline /></NIcon>
        </template>
      </NInput>
    </div>

    <!-- 数据集列表 -->
    <NCard>
      <!-- 数据集标签页 -->
      <NTabs v-model:value="tabValue" type="line" size="large" :tabs-padding="20" pane-style="padding: 20px;">
        <NTabPane name="all" tab="全部数据集">
          <!-- 数据集表格 -->
          <NDataTable
            :columns="columns"
            :data="datasets"
            :row-props="rowProps"
            :bordered="false"
            :single-line="false"
          />

          <!-- 分页 -->
          <div class="flex items-center justify-between border-t p-4">
            <div class="text-sm text-gray-700">
              显示 {{ (page - 1) * pageSize + 1 }} 到 {{ Math.min(page * pageSize, totalItems) }} 条， 共
              {{ totalItems }} 条
            </div>
            <NPagination
              v-model:page="page"
              :page-size="pageSize"
              :item-count="totalItems"
              @update:page="handlePageChange"
            />
          </div>
        </NTabPane>
        <NTabPane name="my" tab="我的数据集" />
        <NTabPane name="public" tab="公开数据集" />
        <NTabPane name="archived" tab="已归档" />
      </NTabs>
    </NCard>
  </NSpace>
</template>
