<style>
  .demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 120px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 50%;
  }
</style>

<template>
  <div class="app-container">
    <div class="filter-container">
      <el-form  :inline="true">
        <el-form-item label="设备号">
          <el-input v-model="listQuery.pileId"></el-input>
        </el-form-item>
        <el-form-item label="充电卡号">
          <el-input v-model="listQuery.chargeCardId"></el-input>
        </el-form-item>
         
        <el-form-item>
          <el-button type="primary" @click="handleFilter">查询</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table :data="list" v-loading.body="listLoading" element-loading-text="拼命加载中" border fit  highlight-current-row  @sort-change="handleSortChange"v-if="hasPerm('Public:read')">

      <el-table-column align="left" label="实时时间" prop="createTime" width="80"></el-table-column>
      <el-table-column align="center" label="开启时间" prop="transTime" min-width="80"></el-table-column>
      <el-table-column align="center" label="客户电话" prop="phone" ></el-table-column>
      <el-table-column align="center" label="物理卡号" prop="chargeCardId" min-width="80"></el-table-column>
      <el-table-column align="center" label="网关号" prop="gateId" min-width="50"></el-table-column>
      <el-table-column align="center" label="设备号" prop="pileId" min-width="50"></el-table-column>
      <el-table-column align="center" label="充电口" prop="port" min-width="40">
      </el-table-column>
      <el-table-column align="center" label="流水" prop="transId" min-width="40"></el-table-column>
      <el-table-column align="center" label="充电时长" prop="transMinutes" min-width="40"></el-table-column>
      <el-table-column align="center" label="充电量" prop="chargeKWh" min-width="45"></el-table-column>
      <el-table-column align="center" label="金额" prop="s3Money2" min-width="40"></el-table-column>
      <el-table-column align="center" label="输出电压" prop="outV" min-width="50"></el-table-column>
        <el-table-column align="center" label="输出电流" prop="outI" min-width="50"></el-table-column>
      <el-table-column align="center" label="SOC" prop="SOC" min-width="40"></el-table-column>


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
          pileId: '',
          chargeCardId: ''
        },
        tempData: {
          createTime: '',
          transTime: '',
          phone: '',
          chargeCardId: '',
          gateId: '',
          pileId: '',
          port: '',
          transId: '',
          transMinutes: '',
          chargeKWh: '',
          outV: '',
          outI: '',
          SOC: ''
        }
      }
    },
    created() {
      this.getList();
    },
    methods: {
      getList() {
        //查询列表
        this.listLoading = true;
        this.api({
          url: "/VChargingProc/list",
          method: "get",
          params: this.listQuery
        }).then(data => {
          // console.log(data);
          this.listLoading = false;
          this.list = data.list;
          this.totalCount = data.totalCount;
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
