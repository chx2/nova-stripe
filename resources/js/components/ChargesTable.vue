<template>
    <loading-view :loading="initialLoading">
        <loading-card :loading="loading" class="card relative">
            <div class="overflow-x-auto">
                <table
                        v-if="charges.length > 0"
                        class="table w-full"
                        cellpadding="0"
                        cellspacing="0"
                        data-testid="resource-table"
                >
                    <thead>
                        <tr>
                            <th v-if="columns" v-for="column in columns" class="text-left">
                              <span class="inline-flex items-center capitalize">
                                 {{ column.replaceAll('_', ' ') }}
                              </span>
                            </th>
                        </tr>
                    </thead>
                    <tbody v-for="charge in charges">
                        <tr>
                            <td v-for="column in columns">
                                <span v-if="moneyColumns.find(moneyColumn => moneyColumn === column)">{{ charge.currency | money(charge[column]) }}</span>
                                <span v-else-if="column === 'created'">
                                    {{ date(charge[column]) }}
                                </span>
                                <span v-else-if="column === 'customer'">
                                    <a :href="`/admin/nova-stripe/customers/${charge.customer}`">{{ charge.billing_details.name }}</a>
                                </span>
                                <span v-else-if="column === 'receipt'">
                                    <a :href="charge.receipt_url" target="_blank">
                                        <icon
                                            type="view"
                                            width="22"
                                            height="18"
                                            view-box="0 0 22 16"
                                        />
                                    </a>
                                </span>
                                <span v-else>{{ charge[column] }}</span>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <ChargesPaginationLinks
                :resource="charges"
                :hasMore="hasMore"
                :hasPrevious="hasPrevious"
                @previous="previousPage"
                @next="nextPage"
            ></ChargesPaginationLinks>
        </loading-card>
    </loading-view>
</template>

<script>
import ChargesPaginationLinks from "./PaginationLinks.vue";
import money from "../utils/moneyFormat";

export default {
    components: { ChargesPaginationLinks },
    props: ['columns'],
    data() {
        return {
            charges: {},
            initialLoading: true,
            loading: false,
            hasMore: false,
            page: 1,
            moneyColumns: ['amount', 'amount_captured', 'amount_refunded', 'application_fee_amount', ],
            dateColumns: ['created'],
            statusClassList: {
                'succeeded' : 'bg-success-light text-success-dark',
                'pending' : 'bg-warning-light text-warning-dark',
                'failed' : 'bg-danger-light text-danger-dark',
            }
        }
    },
    computed: {
        hasPrevious() {
            return this.page > 1
        },
    },
    methods: {
        moment: moment,
        date(date) {
            return moment.unix(date).format("YYYY/MM/DD h:mm:ss a");
        },
        listCharges(params) {
            Nova.request()
                .get("/nova-vendor/nova-stripe/stripe/charges", { params })
                .then((response) => {
                    this.charges = response.data.charges.data
                    this.charges.length > 0 ? this.$emit('charge', this.charges[0]) : '';
                    this.hasMore = response.data.charges.has_more
                    this.initialLoading = false
                    this.loading = false
                })
        },
        nextPage() {
            this.loading = true;

            this.listCharges({
                starting_after: this.charges[this.charges.length - 1].id,
            });

            this.page++;
        },
        previousPage() {
            this.loading = true;

            this.listCharges({ ending_before: this.charges[0].id });

            if (this.hasPrevious) {
                this.page--;
            }
        },
        statusClass(status) {
            return this.statusClassList[status];
        },
    },
    filters: {
        date(date) {
            return moment.unix(date).format("YYYY/MM/DD h:mm:ss a");
        },

        money,
    },
    created() {
        this.listCharges();
    },
};
</script>
