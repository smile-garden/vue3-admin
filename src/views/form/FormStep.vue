<template>
  <a-card>
    <a-steps :current="current" class="mb10">
      <a-step
        v-for="item in steps"
        :key="item.title"
        :title="item.title"
        :description="item.des"
      />
    </a-steps>
    <div class="step-content">
      {{ steps[current].content }}
    </div>
    <div class="step-action">
      <a-space>
        <a-button
          v-if="current < steps.length - 1"
          type="primary"
          @click="next"
          >Next</a-button>
        <a-button v-if="current == steps.length - 1" type="primary">Done</a-button>
        <a-button v-if="current > 0" @click="prev">Previous</a-button>
      </a-space>
    </div>
  </a-card>
  <a-card>
    <a-button type="primary" @click="showModal">选择</a-button>
    <list-table
      class="mt10"
      :loading="loading"
      :list="selectedList"
    />
  </a-card>

  <select-modal
    ref="modalRef"
    :getCheckboxProps="getCheckboxProps"
    @save="save"
  />
</template>

<script setup>
import { ref, reactive } from 'vue';
import SelectModal from './components/SelectModal.vue';
import ListTable from './components/ListTable.vue';

const loading = ref(false);
const selectedIdList = ref([]);
const selectedList = ref([]);

const modalRef = ref(null);
const showModal = () => {
  modalRef.value.show();
};
const getCheckboxProps = (record) => ({
  disabled: selectedIdList.value.includes(record.id),
});

const save = (selectedRowKeys, selectedRows, cb) => {
  selectedIdList.value = [...selectedRowKeys, ...selectedIdList.value];
  selectedList.value = [...selectedRows, ...selectedList.value];
  cb();
};

const current = ref(0);
const steps = reactive([
  {
    title: 'One',
    content: 'One content',
    des: '111',
  },
  {
    title: 'Two',
    content: 'Two content',
    des: '222',
  },
  {
    title: 'Three',
    content: 'Three content',
    des: '333',
  },
]);

const next = () => {
  current.value += 1;
};

const prev = () => {
  current.value -= 1;
};
</script>

<style lang="less" scoped>
.step {
  &-content {
    height: 160px;
    background: #efefef;
    line-height: 160px;
    text-align: center;
  }

  &-action {
    margin-top: 10px;
    text-align: center;
  }
}
</style>
