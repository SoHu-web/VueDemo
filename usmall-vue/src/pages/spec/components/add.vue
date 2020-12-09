<template>
  <div>
    <el-dialog :title="info.title" :visible.sync="info.isShow">
      <el-form :model="form">
        <el-form-item label="规格名称" :label-width="width">
          <el-input v-model="form.specsname" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item
          label="规格属性"
          :label-width="width"
          v-for="(item, index) in arrAttr"
          :key="index"
        >
          <el-row>
            <el-col :span="17">
              <el-input v-model="item.value" autocomplete="off"></el-input>
            </el-col>
            <el-col :span="4"
              ><el-button
                type="primary"
                class="add"
                v-if="index == 0"
                @click="addAttr"
                >新增规格属性</el-button
              >
              <el-button type="danger" class="add" v-else @click="delAttr"
                >删除</el-button
              ></el-col
            >
          </el-row>
        </el-form-item>

        <el-form-item label="状态" :label-width="width">
          <el-switch
            v-model="form.status"
            active-color="#13ce66"
            inactive-color="#ff4949"
            :active-value="1"
            :inactive-value="2"
          >
          </el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="info.isShow = false">取 消</el-button>
        <el-button type="primary" @click="add" v-if="info.isAdd"
          >添 加</el-button
        >
        <el-button type="primary" @click="update" v-else>修 改</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { mapActions, mapGetters } from "vuex";
import { reqspecsAdd, reqspecsListOne,reqspecsEdit } from "../../../util/request";
export default {
  props: ["info"],
  computed: {
    ...mapGetters({}),
  },
  components: {},
  data() {
    return {
      width: "160px",
      form: {
        specsname: "",
        attrs: "",
        status: 1,
      },
      arrAttr: [
        {
          value: "",
        },
      ],
    };
  },
  methods: {
    // 重置
    empty() {
      this.form = {
        specsname: "",
        attrs: "",
        status: 1,
      };
    },
    // 隐藏弹框
    hide() {
      this.info.isShow = false;
    },
    //添加
    add() {
      //   console.log(this.$refs.tree.getCheckedKeys());
      // 由于后端要的数据是字符串数组，而获取到的是数组，所以需要stringify转换一下
      //   this.form.menus = JSON.stringify(this.$refs.tree.getCheckedKeys());
      this.form.attrs = JSON.stringify(
        this.arrAttr.map((item) => {
          return item.value;
        })
      );
      reqspecsAdd(this.form).then((res) => {
        this.empty();
        this.hide();
      });
    },
    addAttr() {
      this.arrAttr.push({
        value: "",
      });
    },
    delAttr(index) {
      this.arrAttr.splice(index, 1);
    },
    ...mapActions({
      requestMenuList: "menu/requestMenuList",
      requestRoleList: "role/requestRoleList",
    }),
    // 获取一条数据
    look(id) {
      reqspecsListOne({ id: id }).then((res) => {
        this.form = res.data.list[0];
        this.form.id =id;
        this.arrAttr = JSON.parse(this.form.attrs).map(item=>{return{value:item}})
      
      });
    },
    update() {
      this.form.attrs = JSON.stringify(this.arrAttr.map(item=>{return item.value}));
      reqspecsEdit(this.form).then((res) => {
        this.requestRoleList();
        this.hide();
      });
    },
  },
  mounted() {
    // if (this.menuList.length == 0) {
    //   this.requestMenuList();
    // }
    // console.log(this.menuList);
  },
};
</script>
<style scoped>
.add {
  margin-left: 10px;
}
</style>