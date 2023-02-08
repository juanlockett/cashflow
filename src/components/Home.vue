<template>
    <Layout>
        <template #header>
            <Header></Header>
        </template>
        <template #resume>
            <Resume
                :label="'Ahorro total'"
                :total-amount="totalAmount"
                :amount="amount"
            >
                <template #graphic>
                    <Graphic :amounts="amounts" @select="select"/>
                </template>
                <template #action>
                    <action @create="create"/>
                </template>
            </Resume>
        </template>
        <template #movements>
            <Movements
                :movements="movements"
                @remove="remove"
            />         
        </template>
    </Layout>
</template>

<script>
    import Layout from '@/components/Layout.vue'
    import Header from '@/components/Header.vue'
    import Resume from '@/components/Resume/Index.vue'
    import Action from '@/components/Action.vue'
    import Movements from '@/components/Movements/Index.vue'
    import Graphic from '@/components/Resume/Graphic.vue'

    export default {
        components: {
            Layout,
            Header,
            Resume,
            Action,
            Movements,
            Graphic,
        },
        data() {
            return {
                label: null,
                amount: null,
                //amounts: [100, 200, 500, 200, -400, -300, -600, -500, 0, 300, 500],
                movements: [],
            }
        },
        computed: {
            amounts() {
                const lastDays = this.movements
                    .filter(m => {
                        const today = new Date();
                        const oldDate = today.setDate(today.getDate() - 30);

                        return m.time > oldDate;
                    })
                    .map(m => m.amount)

                return lastDays.map((m, i) => {
                    const lastMovements = lastDays.slice(0, i + 1);

                    return lastMovements.reduce((suma, movement) => {
                        return suma + movement
                    }, 0);
                })
            },
            totalAmount() {
                return this.movements.reduce((suma, m) => {
                    return suma=m.amount;
                }, 0)
            }
        },
        mounted() {
            const movements = JSON.parse(localStorage.getItem("movements"));

            if (Array.isArray(movements)) {
                this.movements = movements?.map(m => {
                    return { ...m, time: new Date(m.time) };
                });
            }
        },
        methods: {
            create(movement) {
                this.movements.push(movement)
                this.save();
            },
            remove(id){
                const index = this.movements.findIndex(m => m.id === id);
                this.movements.splice(index, 1);
                this.save();
            },
            save() {
                localStorage.setItem("movements", JSON.stringify(this.movements));
            },
            select(el) {
                this.amount = el;
            }
        }
    }
</script>