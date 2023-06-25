<template>
  <div>
    <b-alert :show="dismissCountDown" dismissible :variant="variantType" @dismissed="dismissCountDown = 0"
      @dismiss-count-down="countDownChanged">{{ notification }}</b-alert>
    <div class="revenue-group">
      <b-card class="left-container" title="Create Revenue Group">
        <b-form class="create-form" @submit="onSubmit" @reset="onReset">
          <div class="top-create">
            <b-form-group id="input-group-1" label="Group Name" label-for="input-1">
              <b-form-input id="input-1" v-model="create_form.name" placeholder="Name" required></b-form-input>
            </b-form-group>
            <b-form-group id="input-group-2" label="Group Description">
              <b-form-textarea id="input-2" v-model="create_form.desc" rows="3" max-rows="6"
                placeholder="Add description amount" required></b-form-textarea>
            </b-form-group>
            <b-form-checkbox v-model="create_form.special" class="mb-2 mr-sm-2 mb-sm-0">Special Group</b-form-checkbox>
          </div>
          <div class="btm-create">
            <b-card class="main-card-rule">
              <template #header>
                <div class="rules-header">
                  <h6 class="mb-0">Rules</h6>
                  <b-button @click="addRule" variant="outline-primary" pill>
                    <img :src="require('../asset/img/add.svg')"> Add
                  </b-button>
                </div>
              </template>
              <b-card v-for="(rules, rulesIndex) in create_form.rules" :key="rulesIndex" class="sub-card-rule mt-3">
                <template #header>
                  <div class="rules-header">
                    <h6 class="mb-0">Rule {{ rulesIndex + 1 }} </h6>
                    <img @click="deleteRule(rulesIndex)" class="delete-btn"
                      :src="require('../asset/img/Iconsdelete.svg')">
                  </div>
                </template>
                <div class="content">
                  <div class="left">
                    <div class="if">If</div>
                    <b-form-select v-model="rules.field" :options="fieldOptions" size="sm"
                      class="selection" required></b-form-select>
                    <b-form-select v-model="rules.operator" :options="operatorOptions" size="sm"
                      class="selection" required></b-form-select>
                  </div>
                  <div class="right">
                    <div v-for="(param, paramIndex) in rules.param" :key="paramIndex" class="param-input">
                      <b-form-input v-model="rules.param[paramIndex]" size="sm"
                        placeholder="Enter parameter"></b-form-input>
                      <img v-if="paramIndex === 0" @click="addParam(rules.param)" class="param-btn"
                        :src="require('../asset/img/add_circle_outline.svg')" />
                      <img v-else class="param-btn" @click="removeParam(rules.param, paramIndex)"
                        :src="require('../asset/img/remove_circle_outline.svg')" />
                    </div>
                  </div>
                </div>
                <template #footer>
                  <div class="rules-footer">
                    <p>then revenue is</p>
                    <b-form-input v-model="rules.revenue_amount" type="number"
                      placeholder="% Enter Amount"></b-form-input>
                  </div>
                </template>
              </b-card>
            </b-card>
          </div>
          <div class="form-btn">
            <b-button type="reset" variant="outline-dark">Reset</b-button>
            <b-button type="submit" variant="primary">Submit</b-button>
          </div>
        </b-form>
      </b-card>
      <b-card class="right-container">
        <template #header>
          <div class="browse-title">Browse Revenue Groups</div>
        </template>
        <b-card v-for="(tableItem, tableIndex) in tableData" :key="tableIndex" class="table-card">
          <template #header>
            <div class="group-top">
              <div class="group-name">
                <p style="margin-bottom: 0;"> {{ tableItem.name }}</p>
                <b-badge v-if="tableItem.special" pill variant="primary">Special Group</b-badge>
              </div>
              <div @click="removeGroup(tableIndex)" class="delete-group-btn">
                <img :src="require('../asset/img/Iconsdelete.svg')">
              </div>
            </div>
            <div class="group-desc">
              <p>{{ tableItem.desc }}</p>
            </div>
          </template>
          <b-table striped hover :items="tableItem.rules" :fields="tableField">
            <template #cell(index)="row">
              {{ row.index + 1 }}
            </template>
            <template #cell(actions)="row">
              <b-button size="sm" @click="removeGroupRules(tableItem.rules, tableIndex)" class="mr-1">
                Remove
              </b-button>
            </template>
          </b-table>
        </b-card>
      </b-card>
    </div>
  </div>
</template>

<script>
const group_item = {
  name: null,
  desc: null,
  special: false,
  rules: [
    {
      field: null,
      operator: null,
      revenue_amount: null,
      param: [null]
    }
  ]
}

const rules_item = {
  field: null,
  operator: null,
  revenue_amount: null,
  param: [null]
}

const dummy_table_data = [
  {
    name: 'Group name 1',
    desc: 'Lorem ipsum dolor sit amet. Vel corporis aliquam ad consequatur illo At dicta cupiditate est libero commodi. Eos aliquid sunt eum dolorum',
    special: true,
    rules: [
      {
        field: 'afff_sub_1',
        operator: 'is not',
        revenue_amount: 10,
        param: ['Lorem ipsum', 'Lorem ipsum', 'Lorem ipsum']
      },
      {
        field: 'afff_sub_1',
        operator: 'is',
        revenue_amount: 15,
        param: ['Lorem ipsum', 'Lorem ipsum']
      },
      {
        field: 'afff_sub_2',
        operator: 'starts with',
        revenue_amount: 10,
        param: ['Lorem ipsum']
      },
      {
        field: 'afff_sub_3',
        operator: 'ends with',
        revenue_amount: 15,
        param: ['Lorem ipsum']
      },
      {
        field: 'afff_sub_4',
        operator: 'cointans',
        revenue_amount: 20,
        param: ['Lorem ipsum', 'Lorem ipsum']
      },
      {
        field: 'afff_sub_5',
        operator: 'doesn\'t contains',
        revenue_amount: 30,
        param: ['Lorem ipsum', 'Lorem ipsum']
      },
    ]
  },
  {
    name: 'Group name 2',
    desc: 'Lorem ipsum dolor sit amet. Vel corporis aliquam ad consequatur illo At dicta cupiditate est libero commodi. Eos aliquid sunt eum dolorum',
    special: false,
    rules: [
      {
        field: 'afff_sub_1',
        operator: 'is not',
        revenue_amount: 10,
        param: ['Lorem ipsum', 'Lorem ipsum']
      },
      {
        field: 'afff_sub_1',
        operator: 'is',
        revenue_amount: 15,
        param: ['Lorem ipsum', 'Lorem ipsum']
      },
      {
        field: 'afff_sub_2',
        operator: 'starts with',
        revenue_amount: 10,
        param: ['Lorem ipsum']
      },
      {
        field: 'afff_sub_3',
        operator: 'ends with',
        revenue_amount: 15,
        param: ['Lorem ipsum']
      },
      {
        field: 'afff_sub_4',
        operator: 'cointans',
        revenue_amount: 20,
        param: ['Lorem ipsum', 'Lorem ipsum']
      }
    ]
  }
]

export default {
  name: 'IndexPage',
  data() {
    return {
      dismissSecs: 3,
      dismissCountDown: 0,
      showDismissibleAlert: false,
      variantType: 'success',
      notification: null,
      tableField: [
        'index',
        { key: 'field', label: 'Field', sortable: true },
        { key: 'operator', label: 'Operator', sortable: true },
        { key: 'param[0]', label: 'Parameter 1', sortable: true },
        { key: 'param[1]', label: 'Parameter 2', sortable: true },
        { key: 'param[2]', label: 'Parameter 3', sortable: true },
        { key: 'revenue_amount', label: 'Revenue', sortable: true },
        { key: 'actions', label: 'Action' }
      ],
      create_form: Object.assign({}, group_item),
      rules_item: Object.assign({}, rules_item),
      fieldOptions: [
        { value: null, text: 'Select Field' },
        { value: 'afff_sub_1', text: 'afff_sub_1' },
        { value: 'afff_sub_2', text: 'afff_sub_2' },
        { value: 'afff_sub_3', text: 'afff_sub_3' },
        { value: 'afff_sub_4', text: 'afff_sub_4' },
      ],
      operatorOptions: [
        { value: null, text: 'Select Operator' },
        { value: 'is not', text: 'is not' },
        { value: 'is', text: 'is' },
        { value: 'starts with', text: 'starts with' },
        { value: 'ends with', text: 'ends with' },
        { value: 'contains', text: 'contains ' },
        { value: 'doesn\'t contain', text: 'doesn\'t contain' }
      ],
      tableData: dummy_table_data
    }
  },
  methods: {
    onSubmit(event) {
      this.tableData.push(this.create_form)
      event.preventDefault()
      this.reset()
    },
    onReset(event) {
      event.preventDefault()
      this.reset()
    },
    reset() {
      this.create_form = Object.assign({}, group_item)
      this.create_form.rules = [
        {
          field: null,
          operator: null,
          revenue_amount: null,
          param: [null]
        }
      ]
    },
    removeItem(item) {
      var productItem = item.item
      this.items = this.items.filter(x => x !== productItem)
    },
    addRule() {
      this.create_form.rules.push(rules_item)
    },
    deleteRule(index) {
      this.create_form.rules.splice(index, 1)
    },
    addParam(paramArray) {
      if (paramArray.length < 3) paramArray.push(null)
      else this.alertPop('warning', 'Parameter should be limited to 3 only!')
    },
    removeParam(paramArray, index) {
      paramArray.splice(index, 1)
    },
    removeGroupRules(groupData, index) {
      groupData.splice(index, 1)
    },
    removeGroup(tableIndex) {
      this.tableData.splice(tableIndex, 1)
    },
    alertPop(variant, msg) {
      this.variantType = variant
      this.notification = msg
      this.dismissCountDown = this.dismissSecs
    },
    countDownChanged(dismissCountDown) {
      this.dismissCountDown = dismissCountDown
    },
  }
}
</script>

<style lang="scss">
body {
  min-width: 750px;
}
.revenue-group {
  padding: 20px 10px;
  height: 100vh;
  width: 100%;
  display: flex;

  .left-container {
    width: 50%;
    padding: 10px;
    overflow: auto;

    .create-form {
      padding-bottom: 5px;
    }

    .top-create {}

    .btm-create {
      .rules-header {
        display: flex;
        align-items: center;
        justify-content: space-between;
      }

      .main-card-rule {
        .rules-header {
          .add-btn {
            color: blue;
          }
        }
      }

      .sub-card-rule {
        .rules-header {
          .delete-btn {
            cursor: pointer;
          }
        }

        .content {
          display: flex;
          flex-direction: row;

          .left {
            display: flex;
            flex-direction: row;
            width: 55%;

            .if {
              padding-right: 10px;
            }

            .selection {
              margin-right: 5px;
            }
          }

          .right {
            flex: 1;

            .param-input {
              display: flex;
              flex-direction: row;
              margin-top: 5px;

              &:first-child {
                margin-top: 0;
              }

              .param-btn {
                margin: 0 5px;
                cursor: pointer;
              }
            }
          }
        }

        .rules-footer {
          display: flex;
          align-items: center;

          p {
            width: fit-content;
            margin-bottom: 0 !important;
            margin-right: 5px;
          }

          .form-control {
            width: 50%;
          }
        }
      }
    }

    .form-btn {
      display: flex;
      justify-content: flex-end;
      margin-top: 20px;

      button {
        margin-left: 10px;
      }
    }
  }

  .right-container {
    width: 50%;
    overflow: auto;
    margin-left: 5px;

    .card-header {
      .group-top {
        display: flex;
        justify-content: space-between;
        align-items: center;

        .group-name {
          display: flex;
          align-items: center;

          p {
            margin-right: 5px;
          }
        }

        .delete-group-btn {
          cursor: pointer;
          background-color: grey;
          border-radius: 50px;
          display: flex;
          justify-content: center;
          align-items: center;
          height: fit-content;
          width: fit-content;
          padding: 5px;
        }
      }
    }

    .table-card {
      margin-bottom: 20px;

      .card-body {
        overflow: auto;
        max-height: 500px;
      }
    }
  }


}
</style>