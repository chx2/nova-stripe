<template>
    <div>
        <heading class="mb-6">Transactions</heading>

        <card>
            <ColumnSelect
                v-model="selectedColumns"
                :resource="singleCharge"
                @checkedColumns="selectedColumns = $event"
            />

            <ChargesTable @charge="singleCharge = $event" :columns="selectedColumns"/>
        </card>
    </div>
</template>

<script>
import ChargesTable from '../components/ChargesTable.vue';
import ColumnSelect from "../components/ColumnSelect";

export default {
    components: { ChargesTable, ColumnSelect },
    data() {
        return {
            selectedColumns: ['customer', 'amount', 'created', 'receipt'],
            singleCharge: {},
        }
    },
}
</script>
