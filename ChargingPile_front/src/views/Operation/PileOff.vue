<template>
  <div class="app-container">
    <div class="filter-container">
      <el-form  :inline="true">
        <el-select v-model="listQuery.stationId" placeholder="请选择">
            <el-option
              v-for="item in stationNames"
              :key="item.id"
              :label="item.stationName"
              :value="item.stationId">
            </el-option>
          </el-select>
        <el-form-item>
          <el-button type="primary" @click="handleFilter">查询</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table :data="list" v-loading.body="listLoading" element-loading-text="拼命加载中" border fit  highlight-current-row  @sort-change="handleSortChange"v-if="hasPerm('Public:read')">

      <el-table-column align="left" label="序号" prop="id" sortable="custom" width="80" fixed></el-table-column>
      <el-table-column align="center" label="所属充电站" prop="address" min-width="60"></el-table-column>
      <el-table-column align="center" label="网关号" prop="gateId" min-width="80"></el-table-column>
      <el-table-column align="center" label="桩号" prop="pileNumber" min-width="30"></el-table-column>
      <el-table-column align="center" label="桩编码" prop="pileId" min-width="80"></el-table-column>
      <el-table-column align="center" label="类型" prop="typeName" min-width="40"></el-table-column>
      <el-table-column align="center" label="运行状态" min-width="30"></el-table-column>
      <el-table-column align="center" label="上报时间"  prop="stateUpdateTime" min-width="30">
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="listQuery.pageNum"
      :page-size="listQuery.pageRow"
      :total="totalCount"
      :page-sizes="[10, 20, 50, 100]"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
  </div>
</template>
<script>

  export default {
    data() {
      return {
        totalCount: 0, //分页组件--数据总条数
        list: [],//表格的数据
        listLoading: false,//数据加载等待动画
        listQuery: {
          pageNum: 1,//页码
          pageRow: 20,//每页条数
          stationId: ''
        },
        tempData: {
          id: '',
          stationId: '',
          address: '',
          gateId: '',
          pileNumber: '',
          pileId: '',
          pileId: '',
          typeName: '',
          stateUpdateTime: ''
        },
        stationNames: [],
        dateInfo: {}
      }
    },
    created() {
      this.getList();
      this.getStationInfo();
    },
    methods: {
      getList() {
        //查询列表
        this.listLoading = true;
        console.log(this.listQuery);
        this.api({
          url: "ChargingPile/getPileOff",
          method: "get",
          params: this.listQuery
        }).then(data => {
          console.log(data);
          this.listLoading = false;
          this.list = data.list;
          this.totalCount = data.totalCount;
        })
      },
      getStationInfo(){
        this.api({
          url: "/ChargingPile/getStationInfo",
          method: "get"
        }).then(data => {
          this.stationNames = data.list;
          console.log(this.stationNames);
        })
      },
       handleSortChange(val) {
        // 排序
        this.listQuery.order = val.order === 'descending' ? 'DESC' : 'ASC';
        // console.log(this.listQuery);
        this.handleFilter();
      },
      handleSizeChange(val) {
        //改变每页数量
        this.listQuery.pageRow = val;
        this.handleFilter();
      },
      handleCurrentChange(val) {
        //改变页码
        this.listQuery.pageNum = val;
        this.getList();
      },
      handleFilter() {
        //查询事件
        this.listQuery.pageNum = 1;
        this.getList();
      },
      getIndex($index) {
        //表格序号
        return (this.listQuery.pageNum - 1) * this.listQuery.pageRow + $index + 1
      }
    }
  }
</script>
