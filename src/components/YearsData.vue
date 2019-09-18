<template>
  <div class=hello>
    <h1>{{ msg }}</h1>
    <div>
      <h3 class="my-4">Years data</h3>
      <div class="d-flex p-5 align-items-start justify-content-center flex-column col-10 mx-auto">
        <div class="border m-3 col-12 p-3"
             v-for="(company, index) in data" :key="company.year">
          <!-- Collapse controller-->
          <div class="text-right">
            <button class="border-0 bg-transparent" :aria-expanded="company.showCollapsed"
                    :aria-controls="`${company.year}`"
                    v-b-toggle[company.year]
                    @click="collapse(index)">
              <i class="fa" :class="{'fa-caret-up': company.showCollapsed,
           'fa-caret-down': !company.showCollapsed}"></i>
            </button>
          </div>

        <!-- Collapsed information   -->
          <div class="d-flex justify-content-between align-items-center" key="collapsed">
            <h4 class="year-header my-0">{{company.year}}
              <small class="text-muted d-block">Balance</small>
            </h4>
            <transition-group mode="out-in" name="fade">
              <p class="font-weight-bold mx-3 my-0 d-inline-block" key="active"
                 v-if="!company.showCollapsed">
                <small class="font-weight-light">Active: </small>
                {{company.balance.active.sum | suffixFormat}}</p>
              <p class="font-weight-bold mx-3 my-0 d-inline-block" key="passive"
                 v-if="!company.showCollapsed">
                <small class="font-weight-light">Passive: </small>
                {{company.balance.passive.sum | suffixFormat}}</p>
            </transition-group>
          </div>

          <b-collapse :id="`${company.year}`" v-model="company.showCollapsed"
                      accordion="my-accordion">
            <div class="col-12">
              <h6 class="text-right my-3"><span class="font-weight-light">Active:</span>
                {{company.balance.active.sum | formatNumber}}</h6>
              <table class="text-left table">
                <tr class="balance-element"
                    @click="openDetail('Fixed', company.balance.active.fixed_assets)">
                  <td class="balance-label">Fixed</td>
                  <td class="font-weight-bold">
                    {{company.balance.active.fixed_assets.sum | formatNumber}}</td>
                </tr>
                <tr class="balance-element"
                    @click="openDetail('Liquid', company.balance.active.liquid_assets)">
                  <td class="balance-label">Liquid</td>
                  <td class="font-weight-bold">
                    {{company.balance.active.liquid_assets.sum | formatNumber}}</td>
                </tr>
                <tr class="balance-element"
                    @click="openDetail('Deferred Items', company.balance.active.deferred_items)">
                  <td class="balance-label">Deferred Items</td>
                  <td class="font-weight-bold">
                    {{company.balance.active.deferred_items.sum | formatNumber}}</td>
                </tr>
                <tr class="balance-element"
                    @click="openDetail('Deferred Taxes', company.balance.active.deferred_taxes)">
                  <td class="balance-label">Deferred Taxes</td>
                  <td class="font-weight-bold">
                    {{company.balance.active.deferred_taxes.sum | formatNumber}}</td>
                </tr>
                <tr class="balance-element"
                    @click="openDetail('Active Difference', company.balance.active.active_difference)">
                  <td class="balance-label">active difference</td>
                  <td class="font-weight-bold">
                    {{company.balance.active.active_difference.sum | formatNumber}}</td>
                </tr>
                <tr v-if="company.balance.active.other.length">
                  <td class="balance-label">other</td>
                  <td class="font-weight-bold">
                    {{company.balance.active.other | formatNumber}}</td>
                </tr>
              </table>
            </div>
            <div class="col-12">
              <h6 class="text-right my-3"><span class="font-weight-light">Passive:</span>
                {{company.balance.passive.sum | formatNumber}}</h6>
              <table class="text-left table">
                <tr class="balance-element"
                    @click="openDetail('Equity', company.balance.passive.equity)">
                  <td class="balance-label">Equity</td>
                  <td class="font-weight-bold">
                    {{company.balance.passive.equity.sum | formatNumber}}</td>
                </tr>
                <tr class="balance-element"
                    @click="openDetail('Accruals', company.balance.passive.accruals)">
                  <td class="balance-label">accruals</td>
                  <td class="font-weight-bold">
                    {{company.balance.passive.accruals.sum | formatNumber}}</td>
                </tr>
                <tr class="balance-element"
                    @click="openDetail('Liabilities', company.balance.passive.liabilities)">
                  <td class="balance-label">liabilities</td>
                  <td class="font-weight-bold">
                    {{company.balance.passive.liabilities.sum | formatNumber}}</td>
                </tr>
                <tr class="balance-element"
                    @click="openDetail('Deferred Taxes', company.balance.passive.deferred_taxes)">
                  <td class="balance-label">Deferred Taxes</td>
                  <td class="font-weight-bold">
                    {{company.balance.passive.deferred_taxes.sum | formatNumber}}</td>
                </tr>
                <tr class="balance-element"
                    @click="openDetail('Deferred Items', company.balance.passive.deferred_items)">
                  <td class="balance-label">Deferred Items</td>
                  <td class="font-weight-bold">
                    {{company.balance.passive.deferred_items.sum | formatNumber}}</td>
                </tr>
                <tr v-if="company.balance.passive.other.length">
                  <td class="balance-label">other</td>
                  <td class="font-weight-bold">{{company.balance.passive.other}}</td>
                </tr>
              </table>
            </div>
          </b-collapse>
        </div>
      </div>
    </div>
    <transition name="fade">
      <div v-if="showDetail" class="modal-backdrop"></div>
    </transition>
    <transition name="expand">
      <detail :title="detailTitle"
              :data="detailData"
              @hide-detail="showDetail = false"
              v-if="showDetail">
      </detail>
    </transition>

  </div>
</template>

<script>
import { BCollapse } from 'bootstrap-vue';
import Detail from './Detail.vue';

export default {
  name: 'YearsData',
  props: {
    msg: String,
  },
  components: {
    BCollapse,
    Detail,
  },
  data() {
    return {
      data: [
        {
          year: 2007,
          balance: {
            active: {
              sum: 19020936.75,
              fixed_assets: {
                sum: 5701397.89,
                immaterial_assets: {
                  sum: 69937,
                  self_created: 0,
                  acquired: 0,
                  goodwill: 0,
                  pre_payments: 0,
                  other: [],
                },
                tangible_assets: {
                  sum: 5511782.59,
                  buildings_and_land: 849461,
                  machines: 2431978,
                  other_tangible_assets: 991461,
                  pre_payments: 1238882.59,
                  other: [],
                },
                financial_assets: {
                  sum: 119678.3,
                  company_participations: 15135,
                  affiliated_company_loans: 0,
                  participations: 0,
                  participation_company_loans: 0,
                  securities: 0,
                  other_financial_assets: 0,
                  other: [
                    {
                      name: 'Rückdeckungsversicherungen',
                      amount: 104543.3,
                    },
                  ],
                },
                other: [],
              },
              liquid_assets: {
                sum: 13259137.86,
                inventory: {
                  sum: 3210872.0199999996,
                  raw_materials: 827211.82,
                  unfinished_products: 230197.4,
                  finished_products: 2153462.8,
                  pre_payments: 0,
                  other: [],
                },
                claims: {
                  sum: 8616345.73,
                  trade_receivables: 6902992.36,
                  affiliated_company_retrievables: 516012.29,
                  participation_company_retrievables: 0,
                  other_claims: 1068393.28,
                  other: [
                    {
                      name: 'Forderungen gegen nahestehende Unternehmen',
                      amount: 128947.8,
                    },
                  ],
                },
                securities: {
                  sum: 0,
                  company_participations: 0,
                  other_securities: 0,
                  other: [],
                },
                cash_holdings: {
                  sum: 1431920.11,
                  other: [],
                },
                other: [],
              },
              deferred_items: {
                sum: 60401,
                other: [],
              },
              deferred_taxes: {
                sum: 0,
                other: [],
              },
              active_difference: {
                sum: 0,
                other: [],
              },
              other: [],
            },
            passive: {
              sum: 19021289.75,
              equity: {
                sum: 10036196.86,
                subscribed_capital: {
                  sum: 3200000,
                  other: [],
                },
                capital_reserve: {
                  sum: 0,
                  other: [],
                },
                revenue_reserve: {
                  sum: 4516981.37,
                  legal_reserve: 0,
                  controlling_company_shares_reserve: 0,
                  statutory_reserve: 0,
                  other_revenue_reserve: 0,
                  other: [],
                },
                profit_carried_over: {
                  sum: 0,
                  other: [],
                },
                yearly_plus: {
                  sum: 2319215.49,
                  other: [],
                },
                other: [],
              },
              accruals: {
                sum: 6443846.96,
                pensions_and_similar: 1656955,
                tax: 1479238,
                other_accruals: 3307653.96,
                other: [],
              },
              liabilities: {
                sum: 2541245.93,
                bonds: 0,
                bank_liabilities: 200000,
                received_advance_payments: 0,
                received_goods: 1126499.85,
                acceptance_and_draft_liabilities: 0,
                affiliated_company_liabilities: 0,
                participation_company_liabilities: 0,
                other_liabilities: 1169194.21,
                other: [
                  {
                    name: 'Verbindlichkeiten gegenüber nahestehenden Unternehmen',
                    amount: 45551.87,
                  },
                ],
              },
              deferred_items: {
                sum: 0,
                other: [],
              },
              deferred_taxes: {
                sum: 0,
                other: [],
              },
              other: [],
            },
          },
        },
        {
          year: 2008,
          balance: {
            active: {
              sum: 21797086.49,
              fixed_assets: {
                sum: 8005582.6,
                immaterial_assets: {
                  sum: 57651,
                  self_created: 0,
                  acquired: 0,
                  goodwill: 0,
                  pre_payments: 0,
                  other: [],
                },
                tangible_assets: {
                  sum: 7800664,
                  buildings_and_land: 4094411,
                  machines: 2260895,
                  other_tangible_assets: 1392858,
                  pre_payments: 52500,
                  other: [],
                },
                financial_assets: {
                  sum: 147267.6,
                  company_participations: 15135,
                  affiliated_company_loans: 0,
                  participations: 0,
                  participation_company_loans: 0,
                  securities: 0,
                  other_financial_assets: 0,
                  other: [
                    {
                      name: 'Rückdeckungsversicherungen',
                      amount: 132132.6,
                    },
                  ],
                },
                other: [],
              },
              liquid_assets: {
                sum: 13703229.34,
                inventory: {
                  sum: 3252898.66,
                  raw_materials: 1193011.79,
                  unfinished_products: 201153.77,
                  finished_products: 1858733.1,
                  pre_payments: 0,
                  other: [],
                },
                claims: {
                  sum: 9922608.86,
                  trade_receivables: 6207053.17,
                  affiliated_company_retrievables: 667722.65,
                  participation_company_retrievables: 0,
                  other_claims: 3047833.04,
                  other: [
                    {
                      name: 'Forderungen gegen nahestehende Unternehmen',
                      amount: 0,
                    },
                  ],
                },
                securities: {
                  sum: 0,
                  company_participations: 0,
                  other_securities: 0,
                  other: [],
                },
                cash_holdings: {
                  sum: 527721.82,
                  other: [],
                },
                other: [],
              },
              deferred_items: {
                sum: 88274.55,
                other: [],
              },
              deferred_taxes: {
                sum: 0,
                other: [],
              },
              active_difference: {
                sum: 0,
                other: [],
              },
              other: [],
            },
            passive: {
              sum: 21797086.490000002,
              equity: {
                sum: 10565590.96,
                subscribed_capital: {
                  sum: 3200000,
                  other: [],
                },
                capital_reserve: {
                  sum: 0,
                  other: [],
                },
                revenue_reserve: {
                  sum: 5116196.86,
                  legal_reserve: 0,
                  controlling_company_shares_reserve: 0,
                  statutory_reserve: 0,
                  other_revenue_reserve: 0,
                  other: [],
                },
                profit_carried_over: {
                  sum: 0,
                  other: [],
                },
                yearly_plus: {
                  sum: 2249394.1,
                  other: [],
                },
                other: [],
              },
              accruals: {
                sum: 5746736,
                pensions_and_similar: 1667742,
                tax: 848205,
                other_accruals: 3230789,
                other: [],
              },
              liabilities: {
                sum: 5484759.529999999,
                bonds: 0,
                bank_liabilities: 1000000,
                received_advance_payments: 0,
                received_goods: 1356099.74,
                acceptance_and_draft_liabilities: 0,
                affiliated_company_liabilities: 0,
                participation_company_liabilities: 0,
                other_liabilities: 1842258.52,
                other: [
                  {
                    name: 'Verbindlichkeiten gegenüber nahestehenden Unternehmen',
                    amount: 1286401.27,
                  },
                ],
              },
              deferred_items: {
                sum: 0,
                other: [],
              },
              deferred_taxes: {
                sum: 0,
                other: [],
              },
              other: [],
            },
          },
        },
        {
          year: 2009,
          balance: {
            active: {
              sum: 21051328.770000003,
              fixed_assets: {
                sum: 9675436.19,
                immaterial_assets: {
                  sum: 23043,
                  self_created: 0,
                  acquired: 0,
                  goodwill: 0,
                  pre_payments: 0,
                  other: [],
                },
                tangible_assets: {
                  sum: 7666845,
                  buildings_and_land: 3939691,
                  machines: 2316872,
                  other_tangible_assets: 1410282,
                  pre_payments: 0,
                  other: [],
                },
                financial_assets: {
                  sum: 1985548.1900000002,
                  company_participations: 1825135.09,
                  affiliated_company_loans: 0,
                  participations: 0,
                  participation_company_loans: 0,
                  securities: 0,
                  other_financial_assets: 0,
                  other: [
                    {
                      name: 'Rückdeckungsversicherungen',
                      amount: 160413.1,
                    },
                  ],
                },
                other: [],
              },
              liquid_assets: {
                sum: 11283949.870000001,
                inventory: {
                  sum: 3397935.62,
                  raw_materials: 1217949.6,
                  unfinished_products: 274871.9,
                  finished_products: 1905114.12,
                  pre_payments: 0,
                  other: [],
                },
                claims: {
                  sum: 6062092.68,
                  trade_receivables: 4454550.79,
                  affiliated_company_retrievables: 634562.76,
                  participation_company_retrievables: 0,
                  other_claims: 972979.13,
                  other: [],
                },
                securities: {
                  sum: 0,
                  company_participations: 0,
                  other_securities: 0,
                  other: [],
                },
                cash_holdings: {
                  sum: 1823921.57,
                  other: [],
                },
                other: [],
              },
              deferred_items: {
                sum: 91942.71,
                other: [],
              },
              deferred_taxes: {
                sum: 0,
                other: [],
              },
              active_difference: {
                sum: 0,
                other: [],
              },
              other: [],
            },
            passive: {
              sum: 21051328.770000003,
              equity: {
                sum: 12216280.21,
                subscribed_capital: {
                  sum: 3200000,
                  other: [],
                },
                capital_reserve: {
                  sum: 0,
                  other: [],
                },
                revenue_reserve: {
                  sum: 7365590.96,
                  legal_reserve: 0,
                  controlling_company_shares_reserve: 0,
                  statutory_reserve: 0,
                  other_revenue_reserve: 0,
                  other: [],
                },
                profit_carried_over: {
                  sum: 0,
                  other: [],
                },
                yearly_plus: {
                  sum: 1650689.25,
                  other: [],
                },
                other: [],
              },
              accruals: {
                sum: 5096767.87,
                pensions_and_similar: 1749901,
                tax: 538531.87,
                other_accruals: 2808335,
                other: [],
              },
              liabilities: {
                sum: 3738280.6900000004,
                bonds: 0,
                bank_liabilities: 800000,
                received_advance_payments: 0,
                received_goods: 788307.92,
                acceptance_and_draft_liabilities: 0,
                affiliated_company_liabilities: 0,
                participation_company_liabilities: 0,
                other_liabilities: 841709.43,
                other: [
                  {
                    name: 'Verbindlichkeiten gegenüber nahestehenden Unternehmen',
                    amount: 1308263.34,
                  },
                ],
              },
              deferred_items: {
                sum: 0,
                other: [],
              },
              deferred_taxes: {
                sum: 0,
                other: [],
              },
              other: [],
            },
          },
        },
        {
          year: 2010,
          balance: {
            active: {
              sum: 23881615.689999998,
              fixed_assets: {
                sum: 9358187.809999999,
                immaterial_assets: {
                  sum: 51802,
                  self_created: 0,
                  acquired: 0,
                  goodwill: 0,
                  pre_payments: 0,
                  other: [],
                },
                tangible_assets: {
                  sum: 7149111.47,
                  buildings_and_land: 3558707,
                  machines: 2317866.11,
                  other_tangible_assets: 1272538.36,
                  pre_payments: 0,
                  other: [],
                },
                financial_assets: {
                  sum: 2157274.34,
                  company_participations: 1965794.84,
                  affiliated_company_loans: 0,
                  participations: 0,
                  participation_company_loans: 0,
                  securities: 0,
                  other_financial_assets: 0,
                  other: [
                    {
                      name: 'Rückdeckungsversicherungen',
                      amount: 191479.5,
                    },
                  ],
                },
                other: [],
              },
              liquid_assets: {
                sum: 14432362.95,
                inventory: {
                  sum: 4116096.91,
                  raw_materials: 1453161.19,
                  unfinished_products: 367615.98,
                  finished_products: 2295319.74,
                  pre_payments: 0,
                  other: [],
                },
                claims: {
                  sum: 10183724.379999999,
                  trade_receivables: 6963474.6,
                  affiliated_company_retrievables: 1725163.09,
                  participation_company_retrievables: 0,
                  other_claims: 1495086.69,
                  other: [],
                },
                securities: {
                  sum: 0,
                  company_participations: 0,
                  other_securities: 0,
                  other: [],
                },
                cash_holdings: {
                  sum: 132541.66,
                  other: [],
                },
                other: [],
              },
              deferred_items: {
                sum: 91064.93,
                other: [],
              },
              deferred_taxes: {
                sum: 0,
                other: [],
              },
              active_difference: {
                sum: 0,
                other: [],
              },
              other: [],
            },
            passive: {
              sum: 23881615.69,
              equity: {
                sum: 12050910.200000001,
                subscribed_capital: {
                  sum: 3200000,
                  other: [],
                },
                capital_reserve: {
                  sum: 0,
                  other: [],
                },
                revenue_reserve: {
                  sum: 7365590.96,
                  legal_reserve: 0,
                  controlling_company_shares_reserve: 0,
                  statutory_reserve: 0,
                  other_revenue_reserve: 0,
                  other: [],
                },
                profit_carried_over: {
                  sum: 1650689.25,
                  other: [],
                },
                yearly_plus: {
                  sum: -165370.01,
                  other: [],
                },
                other: [],
              },
              accruals: {
                sum: 3893620.56,
                pensions_and_similar: 1750150,
                tax: 37870.56,
                other_accruals: 2105600,
                other: [],
              },
              liabilities: {
                sum: 7937084.930000001,
                bonds: 0,
                bank_liabilities: 3768547.13,
                received_advance_payments: 0,
                received_goods: 1834714.88,
                acceptance_and_draft_liabilities: 0,
                affiliated_company_liabilities: 0,
                participation_company_liabilities: 0,
                other_liabilities: 1040372.06,
                other: [
                  {
                    name: 'Verbindlichkeiten gegenüber nahestehenden Unternehmen',
                    amount: 1293450.86,
                  },
                ],
              },
              deferred_items: {
                sum: 0,
                other: [],
              },
              deferred_taxes: {
                sum: 0,
                other: [],
              },
              other: [],
            },
          },
        },
        {
          year: 2011,
          balance: {
            active: {
              sum: 25507516.23,
              fixed_assets: {
                sum: 8830447.58,
                immaterial_assets: {
                  sum: 125481.76,
                  self_created: 0,
                  acquired: 0,
                  goodwill: 0,
                  pre_payments: 0,
                  other: [
                    {
                      name: 'Lizenzen',
                      amount: 125481.76,
                    },
                  ],
                },
                tangible_assets: {
                  sum: 6706370.98,
                  buildings_and_land: 3381058.32,
                  machines: 2217555.49,
                  other_tangible_assets: 1107757.17,
                  pre_payments: 0,
                  other: [],
                },
                financial_assets: {
                  sum: 1998594.84,
                  company_participations: 1998594.84,
                  affiliated_company_loans: 0,
                  participations: 0,
                  participation_company_loans: 0,
                  securities: 0,
                  other_financial_assets: 0,
                  other: [
                    {
                      name: 'Rückdeckungsversicherungen',
                      amount: 0,
                    },
                  ],
                },
                other: [],
              },
              liquid_assets: {
                sum: 16540739.489999998,
                inventory: {
                  sum: 5461753.91,
                  raw_materials: 1815951.79,
                  unfinished_products: 1005621.22,
                  finished_products: 2640180.9,
                  pre_payments: 0,
                  other: [],
                },
                claims: {
                  sum: 10648426.649999999,
                  trade_receivables: 7068524.38,
                  affiliated_company_retrievables: 2615897.23,
                  participation_company_retrievables: 0,
                  other_claims: 964005.04,
                  other: [],
                },
                securities: {
                  sum: 0,
                  company_participations: 0,
                  other_securities: 0,
                  other: [],
                },
                cash_holdings: {
                  sum: 430558.93,
                  other: [],
                },
                other: [],
              },
              deferred_items: {
                sum: 76129.16,
                other: [],
              },
              deferred_taxes: {
                sum: 60200,
                other: [],
              },
              active_difference: {
                sum: 0,
                other: [],
              },
              other: [],
            },
            passive: {
              sum: 25507516.23,
              equity: {
                sum: 12436104.180000002,
                subscribed_capital: {
                  sum: 3200000,
                  other: [],
                },
                capital_reserve: {
                  sum: 0,
                  other: [],
                },
                revenue_reserve: {
                  sum: 7365590.96,
                  legal_reserve: 0,
                  controlling_company_shares_reserve: 0,
                  statutory_reserve: 0,
                  other_revenue_reserve: 0,
                  other: [],
                },
                profit_carried_over: {
                  sum: 1485319.24,
                  other: [],
                },
                yearly_plus: {
                  sum: 385193.98,
                  other: [],
                },
                other: [],
              },
              accruals: {
                sum: 4944300.5,
                pensions_and_similar: 1685605.5,
                tax: 111071,
                other_accruals: 3147624,
                other: [],
              },
              liabilities: {
                sum: 8127111.550000001,
                bonds: 0,
                bank_liabilities: 3400000,
                received_advance_payments: 0,
                received_goods: 2256388.97,
                acceptance_and_draft_liabilities: 0,
                affiliated_company_liabilities: 102528.02,
                participation_company_liabilities: 0,
                other_liabilities: 1013419.21,
                other: [
                  {
                    name: 'Verbindlichkeiten gegenüber nahestehenden Unternehmen',
                    amount: 1354775.35,
                  },
                ],
              },
              deferred_items: {
                sum: 0,
                other: [],
              },
              deferred_taxes: {
                sum: 0,
                other: [],
              },
              other: [],
            },
          },
        },
        {
          year: 2012,
          balance: {
            active: {
              sum: 25321270.67,
              fixed_assets: {
                sum: 9339593.49,
                immaterial_assets: {
                  sum: 634148.75,
                  self_created: 0,
                  acquired: 0,
                  goodwill: 0,
                  pre_payments: 0,
                  other: [
                    {
                      name: 'Lizenzen',
                      amount: 634148.75,
                    },
                  ],
                },
                tangible_assets: {
                  sum: 6941979.65,
                  buildings_and_land: 3179773.94,
                  machines: 2373202.19,
                  other_tangible_assets: 1178821.6,
                  pre_payments: 210181.92,
                  other: [],
                },
                financial_assets: {
                  sum: 1763465.09,
                  company_participations: 1763465.09,
                  affiliated_company_loans: 0,
                  participations: 0,
                  participation_company_loans: 0,
                  securities: 0,
                  other_financial_assets: 0,
                  other: [],
                },
                other: [],
              },
              liquid_assets: {
                sum: 15822399.600000001,
                inventory: {
                  sum: 5986710.79,
                  raw_materials: 1798148.29,
                  unfinished_products: 1208407.5,
                  finished_products: 2980155,
                  pre_payments: 0,
                  other: [],
                },
                claims: {
                  sum: 8284055.920000001,
                  trade_receivables: 5043044.84,
                  affiliated_company_retrievables: 2490871.08,
                  participation_company_retrievables: 0,
                  other_claims: 731654.56,
                  other: [
                    {
                      name: 'Forderungen gegen nahestehende Unternehmen',
                      amount: 18485.44,
                    },
                  ],
                },
                securities: {
                  sum: 0,
                  company_participations: 0,
                  other_securities: 0,
                  other: [],
                },
                cash_holdings: {
                  sum: 1551632.89,
                  other: [],
                },
                other: [],
              },
              deferred_items: {
                sum: 108877.58,
                other: [],
              },
              deferred_taxes: {
                sum: 50400,
                other: [],
              },
              active_difference: {
                sum: 0,
                other: [],
              },
              other: [],
            },
            passive: {
              sum: 25321270.67,
              equity: {
                sum: 12419898.750000002,
                subscribed_capital: {
                  sum: 3200000,
                  other: [],
                },
                capital_reserve: {
                  sum: 0,
                  other: [],
                },
                revenue_reserve: {
                  sum: 7365590.96,
                  legal_reserve: 0,
                  controlling_company_shares_reserve: 0,
                  statutory_reserve: 0,
                  other_revenue_reserve: 0,
                  other: [],
                },
                profit_carried_over: {
                  sum: 1870513.22,
                  other: [],
                },
                yearly_plus: {
                  sum: -16205.43,
                  other: [],
                },
                other: [],
              },
              accruals: {
                sum: 4190163.63,
                pensions_and_similar: 1837974.4,
                tax: 17748,
                other_accruals: 2334441.23,
                other: [],
              },
              liabilities: {
                sum: 8711208.290000001,
                bonds: 0,
                bank_liabilities: 4118763.44,
                received_advance_payments: 0,
                received_goods: 1850711.09,
                acceptance_and_draft_liabilities: 0,
                affiliated_company_liabilities: 117980.67,
                participation_company_liabilities: 0,
                other_liabilities: 1225193.74,
                other: [
                  {
                    name: 'Verbindlichkeiten gegenüber nahestehenden Unternehmen',
                    amount: 1398559.35,
                  },
                ],
              },
              deferred_items: {
                sum: 0,
                other: [],
              },
              deferred_taxes: {
                sum: 0,
                other: [],
              },
              other: [],
            },
          },
        },
      ],
      detailTitle: '',
      detailData: undefined,
      showDetail: false,
    };
  },
  filters: {
    suffixFormat(number) {
      const num = Number(number);
      if (num >= 1000000000) {
        return `${(num / 1000000000).toFixed(2).replace(/\.0$/, '')} G`;
      }
      if (num >= 1000000) {
        return `${(num / 1000000).toFixed(2).replace(/\.0$/, '')} M`;
      }
      if (num >= 1000) {
        return `${(num / 1000).toFixed(2).replace(/\.0$/, '')} k`;
      }
      return num.toFixed(2);
    },
    formatNumber(number) {
      let formatted;
      formatted = Math.round(number * 100) / 100;
      formatted = formatted.toString().replace('.', ',').replace(/\B(?=(\d{3})+(?!\d))/g, '.');
      return formatted;
    },
  },
  mounted() {
    this.data = this.data.map(obj => ({
      ...obj,
      showCollapsed: false,
    }));
  },
  methods: {
    collapse(index) {
      this.data[index].showCollapsed = !this.data[index].showCollapsed;
    },
    openDetail(title, data) {
      this.detailTitle = title;
      this.detailData = data;
      this.showDetail = true;
    },
  },
};
</script>
<style scoped lang=scss>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
  .balance-element {
    cursor: pointer;
    text-transform: capitalize;
    &:hover{
      .balance-label {
        text-decoration: underline;
      }
      /*background: #eaeef2;*/
    }
  }

.expand {
  transition: all .5s ease-in-out;
  min-width: 50px;
  overflow: hidden;
}

.expand-enter, .expand-leave {
  width: 0;
  opacity: 0;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to  {
  opacity: 0;
}
</style>
