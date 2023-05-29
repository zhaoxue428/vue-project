<!-- 模板结构(必须) -->
<template>
  <!-- html元素；div元素；container-fluid类是流体容器，是Bootstrap框架的一部分 -->
  <div class="container-fluid">
    <!-- html元素；div元素；row类-Bootstrap-用来创建一个行 -->
    <div class="row">
      <!-- html元素；div元素；col-12和my-2两个元素，Bootstrap，用于控制元素的布局和外边距，该元素col即列占12个列空间，即整行的宽度，my-2该元素具有垂直方向上的外边距。具体来说，它将在元素的顶部和底部添加2个单位的外边距；
                page.sum属性发生变化时，自动更新显示新值--->
      <div class="col-12 my-2">{{ page.sum }}</div>

      <!-- 定义div元素；包含b-table组件;
            将该组件的值与Vue实例中的page.list属性绑定;
            事件监听器 @input="changeTableFieldValueAction，监听组件的input事件，并在事件被触发时调用vue实例中定义的changeTableFieldValueAction方法"-->
      <div class="col-12 my-2">
        <b-button
          v-if="!invisible.downloadAction"
          css-class="btn-outline-success"
          label="CSV"
          :tabindex="950"
          :is-disabled="disabled.downloadCsvButton"
          @click="downloadAction"
        />
      </div>
      <div class="col-12 my-2">
        <b-table
          v-if="!invisible.list"
          v-model="page.list"
          table-style="min-width:1800px"
          wrapper-class="b-table-spacer"
          :column="column"
          tabindex="250"
          sticky-header-handy-scroll-mode="sticky-header"
          :error-message="errorMessage"
          show-table-footer="false"
          @input="changeTableFieldValueAction"
        >
          <!--  //<template #summary>表示一个命名插槽（named slot） -->
          <!-- //btn-outline-{color}设置按钮的样式类，以呈现外观为轮廓的绿色按钮 -->
          <template #summary>
            <tr>
              <th colspan="100">
                <button
                  type="button"
                  class="btn btn-outline-success w-100"
                  tabindex="10180"
                  @click="addRowAction"
                >
                  行追加 <i class="fas fa-plus"></i>
                </button>
              </th>
            </tr>
          </template>
          <template #addRowAndDelete_column="slotProps">
            <b-button
              v-if="!invisible.downloadCsvButton"
              css-class="btn-outline-success"
              label="CSV"
              :tabindex="950"
              :is-disabled="disabled.downloadCsvButton"
              @click="downloadAction"
            />
            <b-button
              css-class="btn-danger me-md-4"
              label="行削除红"
              :tabindex="slotProps.tabindex"
              @click="deleteAction(slotProps.rowIndex, slotProps.row)"
            />
            <b-button
              css-class="btn-info me-md-4 m-2"
              label="行削除蓝"
              :tabindex="slotProps.tabindex"
              @click="deleteAction(slotProps.rowIndex, slotProps.row)"
            />
            <b-button
              v-if="!invisible.clearButton"
              css-class="btn-light border"
              label="全部クリア"
              postpend-icon-class="fas fa-backspace"
              :tabindex="930"
              :is-disabled="disabled.saveButton"
              @click="clearAction"
            />

            <b-button
              css-class="btn-success"
              :label="editMode ? '修正' : '解除'"
              :tabindex="slotProps.tabindex + 1"
              :is-disabled="disabled.saveFolderButton"
              data-bs-toggle="modal"
              data-bs-target="#SaveFolderModal"
              @click="editRow(slotProps.rowIndex, slotProps.row)"
            />
          </template>
          <template #filenm_column="slotProps">
            <router-link
              v-if="slotProps.row.filenm != null"
              :to="{
                path: '/AdvertisingOrderDetail',
                query: { filenm: slotProps.row.filenm },
              }"
              >{{ slotProps.row.filenm }}</router-link
            >
          </template>
        </b-table>
      </div>
    </div>
  </div>
</template>

<script>
import { Modal } from "bootstrap";
import Validator from "@/utils/Validator";

export default {
  name: "Test",
  components: {},
  data() {
    return {
      pageName: "WEB_Test",
      inlineMode: false,
      errorMessage: [],
      invisible: {},
      allDisabled: false,
      disabled: {},
      allReadonly: false,
      readonly: {},
      editMode: true,
      page: {
        sum: 0,
        list: [],
      },

      column: [
        {
          label: "No.",
          field: "row",
          type: "rowIndex",
          thStyle: "width:50px",
          tdClass: "text-right",
          csvFlag: true,
        },
        {
          label: "タイトル1",
          field: "filenm",
          type: "routerLink",
          isRequired: true,
          attribute: {},
          type: "text",
          csvFlag: true,
        },
        {
          label: "タイトル2",
          field: "filenm1",
          type: "select",
          isRequired: true,
          attribute: {},
          csvFlag: false,
          option: [
            { value: "1", label: "1 - 雑誌" },
            { value: "2", label: "2 - 書籍" },
          ],
        },

        {
          label: "発行番号",
          field: "no",
          type: "number",
          isRequired: true,
          attribute: {},
          csvFlag: true,
          value: "",
        },
        {
          label: "発行日",
          field: "dt",
          type: "date",
          isRequired: true,
          csvFlag: true,
          attribute: {},
        },

        {
          label: "号数",
          field: "volume",
          type: "select",
          csvFlag: true,
          isRequired: true,
          attribute: {},
        },
        {
          label: "単品金額",
          field: "orderUnitPrice",
          type: "number",
          isRequired: true,
          csvFlag: true,
          attribute: {},
          value: "",
        },
        {
          label: "数量",
          field: "orderQuantity",
          type: "number",
          csvFlag: true,
          isRequired: true,
          attribute: {},
          value: "",
        },
        {
          label: "合計金額",
          field: "allprice",
          type: "number",
          isRequired: true,
          attribute: {},
          csvFlag: true,
        },
        {
          label: "合計金額",
          field: "allprice",
          type: "lable",
          isRequired: true,
          attribute: {},
          csvFlag: true,
        },
        {
          label: "銀行名称",
          field: "bnkNm",
          type: "select",
          invisible: false,
          csvFlag: false,
          attribute: {},
        },
        {
          label: "支店",
          field: "stselectnCd",
          type: "number",
          isRequired: true,
          csvFlag: false,
          isDisabled: true,
          attribute: {},
        },
        {
          label: "",
          type: "",
          field: "addRowAndDelete",
          thStyle: "min-width:90px",
          tdClass: "text-center",
          csvFlag: false,
        },
      ],
    };
  },
  computed: {},
  created() {
    this.initAction();
  },
  methods: {
    // 画面初期化:
    //定义了一个名为 initAction 的方法。
    //当调用此方法时，它会创建两个常量：activityName 和 self。
    //activityName 常量的值被设置为 '初期化'，而 self 常量的值被设置为 this。
    //this 关键字通常指向调用该方法的对象。在这种情况下，它可能是 Vue 组件实例。
    initAction() {
      const activityName = "初期化";
      const self = this;

      // 権限チェック
      self.checkPageAuthority("Spname." + self.$route.query.type);

      self.showLoading();
      //データ取得
      Promise.all([
        self.get(` foldera1`),
        self.logActivity(self.$options.name, activityName),
      ])
        .then(([response, logResult]) => {
          // Log success
          self.logSuccess(self.$options.name, activityName, response);

          // 画面に表示
          self.page.list.splice(0, self.page.list.length, ...response.result);
        })
        .catch((error) => {
          // log error
          self.logError(self.$options.name, activityName, error);
        })
        .finally(() => {
          self.hideLoading();
        });
    },
    // 定义changeTableFieldValueAction方法:接受一个对象作为参数；该对象四个属性
    //四个属性-rowindex为行的索引；row为更改的行的数据即每个单元格的值
    //if了的话，调用calcRowOrderAmount方法
    changeTableFieldValueAction({ field, rowIndex, row, value }) {
      debugger;
      if (field == "orderUnitPrice") {
        this.calcRowOrderAmount(row);
      }
      if (field == "orderQuantity") {
        this.calcRowOrderAmount(row);
      }
    },
    // edit row
    editRow(rowIndex, row) {
      let colArray = this.column.map((e) => e.field + "_disabled");
      this.editMode = !this.editMode;
      colArray.forEach((e) => (row[e] = !this.editMode));
    },
    //创建calcRowOrderAmount方法
    calcRowOrderAmount(row) {
      debugger;

      //
      //            row.allprice = (row.orderUnitPrice ?? 0) * (row.orderQuantity ?? 0);
      //精确计算。js使用大数可能是为了避免浮点数运算中的精度问题。
      //toNumber()函数被调用，将计算结果转换回一个普通的数字。
      row.allprice = this.toBigNumber(row.orderUnitPrice ?? 0)
        .multipliedBy(this.toBigNumber(row.orderQuantity ?? 0))
        .toNumber();

      let sum = 0;
      for (let i = 0; i < this.page.list.length; i++) {
        sum += this.page.list[i].allprice;
      }
      this.page.sum = sum;
    },

    // 保存処理
    saveAction() {
      const activityName = "保存";
      const self = this;

      self.showLoading();

      let rule = {
        list: {
          filenm: ";",
          loginId: ";",
        },
      };

      // エラーメッセージをクリア
      self.errorMessage.splice(0);
      const check = new Validator(self.page, rule);
      if (!check.check()) {
        self.errorMessage = check.getErrors();
        self.hideLoading();
        self.notifyInputError();
        return;
      }
      //保存
      Promise.all([
        self.post(" foldera1/list", self.page.list),
        self.logActivity(self.$options.name, activityName, self.page.list),
      ])
        .then(([response, logResult]) => {
          // Log success
          self.logSuccess(self.$options.name, activityName, response);
          // 画面に再表示
          self.page.list.splice(0, self.page.list.length, ...response.result);
          // 成功メッセージ
          self.notifySuccess(null, self.constants.message.success.saved);
        })
        .catch((error) => {
          // log error
          self.logError(self.$options.name, activityName, error);
        })
        .finally(() => {
          self.hideLoading();
        });
    },
    // Download CSV処理
    downloadAction() {
      this.downloadCsvFile(this.column, this.page.list, "WEB_Test.csv");
    },
    // 行追加処理；向list数组里面添加四个新对象；该list数组时名为page对象的一个属性；第一和第二为空对象；dt=column的lable为発行日
    addRowAction() {
      this.page.list.push({});
    },

    // addRowAction() {
    //     // 全て列オブジェクトを取得し、初期値としてnullをセット
    //     let emptyRow = this.column.reduce((acc, cur) => {
    //         if (cur.field) {
    //             acc[cur.field] = null;
    //         }
    //         return acc;
    //     }, {});

    //     this.page.list.push(emptyRow);
    // },
    // 修正処理
    showDialog() {
      let dialog = Modal.getOrCreateInstance(
        document.querySelector("#SaveFolderModal")
      );
      dialog.show();
    },
    // 削除処理:这段代码定义了一个名为 deleteAction 的方法，
    //它接受一个对象作为参数。
    //该对象具有名为 rowIndex 的属性，表示要删除的元素的索引。
    //当调用此方法时，它会使用 splice 方法从名为 list 的数组中删除索引为 rowIndex 的元素。
    //splice 方法是 JavaScript 数组的一个内置方法，它可以在数组中添加或删除元素。
    //在这种情况下，它用于删除一个元素。该数组是名为 page 的对象的一个属性。
    deleteAction({ rowIndex }) {
      this.page.list.splice(rowIndex, 1);
    },
    // クリア処理:这段代码定义了一个名为 clearAction 的方法。
    //当调用此方法时，它会创建一个名为 self 的常量，并将其值设置为 this。
    //this 关键字通常指向调用该方法的对象
    //全部清除？？
    clearAction() {
      const self = this;

      // エラーメッセージクリア
      self.errorMessage.splice(0);

      // 画面の初期値オブジェクト
      const pageInitValue = {};

      Object.keys(self.page).forEach((key) => {
        if (Array.isArray(self.page[key])) {
          self.page[key].splice(0);
          Object.prototype.hasOwnProperty.call(pageInitValue || {}, key) &&
            self.page[key].push(...pageInitValue[key]);
        } else {
          self.page[key] = null;
          Object.prototype.hasOwnProperty.call(pageInitValue || {}, key) &&
            (self.page[key] = pageInitValue[key]);
        }
      });
    },
  },
};
</script>
<style scoped></style>
