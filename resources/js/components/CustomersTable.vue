<template>
    <loading-view :loading="initialLoading">
        <loading-card :loading="loading" class="card relative">
            <table
                v-if="customers.length > 0"
                class="table w-full"
                cellpadding="0"
                cellspacing="0"
                data-testid="resource-table"
            >
                <thead>
                    <tr>
                        <th class="text-left">
                            <span class="inline-flex items-center">
                                Name
                            </span>
                        </th>
                        <th class="text-left">
                            <span class="inline-flex items-center">
                                Email
                            </span>
                        </th>
                        <th class="text-left">
                            <span class="inline-flex items-center">
                                Address
                            </span>
                        </th>
                        <th>&nbsp;</th>
                    </tr>
                </thead>

                <tbody v-for="customer in customers">
                    <tr>
                        <td>{{ customer.name }}</td>
                        <td>{{ customer.email }}</td>
                        <td>{{ parseAddress(customer.address) }}</td>
                        <td>
                            <span>
                                <router-link
                                    class="cursor-pointer text-70 hover:text-primary mr-3"
                                    :to="{
                                        name: 'customer-detail',
                                        params: {
                                            customerId: customer.id,
                                        },
                                    }"
                                    :title="__('View')"
                                >
                                    <icon
                                        type="view"
                                        width="22"
                                        height="18"
                                        view-box="0 0 22 16"
                                    />
                                </router-link>
                            </span>
                        </td>
                    </tr>
                </tbody>
            </table>

            <div v-else class="text-90">No customers</div>

            <customers-pagination-links
                :resource="customers"
                :hasMore="hasMore"
                :hasPrevious="hasPrevious"
                @previous="previousPage"
                @next="nextPage"
            ></customers-pagination-links>
        </loading-card>
    </loading-view>
</template>

<script>
import CustomersPaginationLinks from "./PaginationLinks.vue";
import money from "../utils/moneyFormat";

export default {
    components: { CustomersPaginationLinks },

    data() {
        return {
            customers: {},
            initialLoading: true,
            loading: false,
            hasMore: false,
            page: 1,
        };
    },

    methods: {
        moment: moment,
        parseAddress(object) {
            return `${object.line1}, ${object.city}, ${object.state} ${object.postal_code}`
        },
        listCustomers(params) {
            Nova.request()
                .get("/nova-vendor/nova-stripe/stripe/customers", { params })
                .then((response) => {
                    this.customers = response.data.customers.data;
                    this.hasMore = response.data.customers.has_more;
                    this.initialLoading = false;
                    this.loading = false;
                });
        },

        nextPage() {
            this.loading = true;

            this.listCustomers({
                starting_after: this.customers[this.customers.length - 1].id,
            });

            this.page++;
        },

        previousPage() {
            this.loading = true;

            this.listCustomers({ ending_before: this.customers[0].id });

            if (this.hasPrevious) {
                this.page--;
            }
        },
    },

    computed: {
        hasPrevious() {
            return this.page > 1;
        },
    },

    filters: {
        date(date) {
            return moment.unix(date).format("YYYY/MM/DD h:mm:ss a");
        },

        money,
    },

    created() {
        this.listCustomers();
    },
};
</script>

<style>
/* Scoped Styles */
</style>
