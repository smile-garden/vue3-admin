<template>
  <a-button type="primary" @click="update">{{ $t('action.update') }}</a-button>
  <div id="area-container"></div>
</template>

<script>
import DataSet from '@antv/data-set';
import { Chart } from '@antv/g2';

export default {
  data() {
    return {
      chart: null,
    };
  },
  mounted() {
    const data = [
      { Date: '22 February', 订阅数: 50000, 月收入: 125000 },
      { Date: '28 February', 订阅数: 60000, 月收入: 150000 },
      { Date: '3 March', 订阅数: 100000, 月收入: 250000 },
      { Date: '20 March', 订阅数: 200000, 月收入: 500000 },
      { Date: '7 April', 订阅数: 250000, 月收入: 625000 },
      { Date: '13 June', 订阅数: 210000, 月收入: 525000 },
    ];

    this.initRender(this.transformData(data));
  },
  methods: {
    transformData(data) {
      const ds = new DataSet();
      const dv = ds
        .createView()
        .source(data)
        .transform({
          type: 'map',
          callback(row) {
            row.range = [row.订阅数, row.月收入];
            return row;
          },
        });

      const dv2 = ds
        .createView()
        .source(data)
        .transform({
          type: 'fold',
          fields: ['订阅数', '月收入'],
          key: 'type',
          value: 'value',
          retains: ['Date'],
        });
      return {
        dv,
        dv2,
      };
    },
    initRender({ dv, dv2 }) {
      const chart = new Chart({
        container: 'area-container',
        autoFit: true,
        height: 500,
        padding: 0,
      });

      chart.scale('Date', {
        range: [0, 1],
        tickCount: 10,
        type: 'timeCat',
        mask: 'MM-DD',
      });
      chart.scale({
        range: {
          nice: true,
          sync: true,
        },
        value: {
          nice: true,
          sync: true,
        },
      });
      chart.axis('value', {
        label: {
          formatter: (text) => text.replace(/(\d)(?=(?:\d{3})+$)/g, '$1,'),
        },
      });
      chart.tooltip({
        showCrosshairs: true,
        shared: true,
      });

      // view1
      const view1 = chart.createView({
        padding: [8, 8, 48, 64],
      });
      view1.data(dv.rows);
      view1.axis(false);
      view1.tooltip(false);
      view1.area().position('Date*range').color('#8d8d8d').style({
        fillOpacity: 0.1,
      });

      // view2
      const view2 = chart.createView({
        padding: [8, 8, 48, 64],
      });
      view2.data(dv2.rows);
      view2.line().position('Date*value').color('type');
      view2.point().position('Date*value').color('type').shape('circle');

      chart.removeInteraction('legend-filter'); // 关闭图例过滤交互

      chart.render();
      this.chart = chart;
      this.view1 = view1;
      this.view2 = view2;
    },
    update() {
      const data = [
        { Date: '22 February', 订阅数: 210000, 月收入: 275000 },
        { Date: '28 February', 订阅数: 60000, 月收入: 140000 },
        { Date: '3 March', 订阅数: 100000, 月收入: 250000 },
        { Date: '20 March', 订阅数: 200000, 月收入: 500000 },
        { Date: '7 April', 订阅数: 250000, 月收入: 625000 },
        { Date: '13 June', 订阅数: 210000, 月收入: 525000 },
      ];
      const { dv, dv2 } = this.transformData(data);
      this.chart.data(dv.rows);
      this.view1.data(dv.rows);
      this.view2.data(dv2.rows);
      this.chart.render();
    },
  },
  beforeUnmount() {
    if (this.chart) this.chart.destroy();
    this.chart = null;
  },
};
</script>
