<template>
    <loading-card :loading="initialLoading" class="mb-6 py-3 px-6">
        <detail-text-field
            :field="{ name: 'ID', value: customer.id }"
        ></detail-text-field>
        <detail-text-field
            :field="{ name: 'Name', value: customer.name }"
        ></detail-text-field>
        <detail-text-field
            :field="{ name: 'Address', value: parseAddress(customer.address) }"
        ></detail-text-field>
        <detail-text-field
            :field="{ name: 'Email', value: customer.email }"
        ></detail-text-field>
        <detail-text-field
            :field="{ name: 'Phone', value: customer.phone }"
        ></detail-text-field>
        <detail-text-field
            :field="{ name: 'Created', value: date(customer.created) }"
        ></detail-text-field>
        <detail-text-field
            :field="{ name: 'Invoice Prefix', value: customer.invoice_prefix }"
        ></detail-text-field>
        <detail-boolean-field
            :field="{ name: 'Livemode', value: customer.livemode }"
        ></detail-boolean-field>
    </loading-card>
</template>

<script>
export default {
    props: ["customerId"],
    data() {
        return {
            customer: {},
            initialLoading: true,
        };
    },
    methods: {
        parseAddress(object) {
            return `${object.line1}, ${object.city}, ${object.state} ${object.postal_code}`
        },
        date(date) {
            return moment.unix(date).format("YYYY/MM/DD h:mm:ss a");
        },
        loadCustomer(id) {
            Nova.request()
                .get(
                    "/nova-vendor/nova-stripe/stripe/customers/" +
                        this.customerId
                )
                .then((response) => {
                    this.customer = response.data.customer;
                    this.initialLoading = false;
                });
        },
    },
    created() {
        this.loadCustomer();
    },
};
</script>
