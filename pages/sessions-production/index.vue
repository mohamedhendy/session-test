<template>
    <div>
        <!-- disable first last pagination -->
        <div class="panel mt-6 pb-0">
            <div class="mb-5 flex flex-col gap-5 md:flex-row md:items-center">
                <h5 class="text-lg font-semibold dark:text-white-light">Disable First Last Pagination</h5>
                <div class="ltr:ml-auto rtl:mr-auto">
                    <input v-model="search" type="text" class="form-input w-auto" placeholder="Search..." />
                </div>
            </div>

            <div class="datatable">
                <vue3-datatable
                    :rows="rows"
                    :columns="cols"
                    :totalRows="rows?.length"
                    :sortable="true"
                    :search="search"
                    skin="whitespace-nowrap bh-table-hover"
                    :showFirstPage="false"
                    :showLastPage="false"
                    firstArrow='<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="w-4.5 h-4.5 rtl:rotate-180"> <path d="M13 19L7 12L13 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/> <path opacity="0.5" d="M16.9998 19L10.9998 12L16.9998 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/> </svg>'
                    lastArrow='<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="w-4.5 h-4.5 rtl:rotate-180"> <path d="M11 19L17 12L11 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/> <path opacity="0.5" d="M6.99976 19L12.9998 12L6.99976 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/> </svg> '
                    previousArrow='<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="w-4.5 h-4.5 rtl:rotate-180"> <path d="M15 5L9 12L15 19" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/> </svg>'
                    nextArrow='<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="w-4.5 h-4.5 rtl:rotate-180"> <path d="M9 5L15 12L9 19" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/> </svg>'
                >
                    <template #action>
                        <div class="flex items-center">
                            <client-only>
                                <div>
                                    <button type="button" class="ltr:mr-2 rtl:ml-2" v-tippy:edit>
                                        <icon-pencil />
                                    </button>
                                    <tippy target="edit">Edit</tippy>
                                </div>
                                <div>
                                    <button type="button" v-tippy:delete>
                                        <icon-trash-lines />
                                    </button>
                                    <tippy target="delete">Delete</tippy>
                                </div>
                            </client-only>
                        </div>
                    </template>
                </vue3-datatable>
            </div>
        </div>
    </div>
</template>
<script>
    import axios from 'axios';
    import Vue3Datatable from '@bhplugin/vue3-datatable';
    import "@bhplugin/vue3-datatable/dist/style.css";
    export default {
        components: {
            Vue3Datatable,
            // CreateNewSession,
            // EditSession,
        },
        data() {
            return {
                addNew: false,
                search: '',
                cols: [
                    { field: 'id', title: 'ID' },
                    { field: 'firstName', title: 'First Name' },
                    { field: 'lastName', title: 'Last Name' },
                    { field: 'email', title: 'Email' },
                    { field: 'phone', title: 'Phone' },
                    { field: 'action', title: 'Action', sort: false },
                ],
                rows: [
                    {
                        id: 1,
                        firstName: 'Caroline',
                        lastName: 'Jensen',
                        email: 'carolinejensen@zidant.com',
                        dob: '2004-05-28',
                        address: {
                            street: '529 Scholes Street',
                            city: 'Temperanceville',
                            zipcode: 5235,
                            geo: {
                                lat: 23.806115,
                                lng: 164.677197,
                            },
                        },
                        phone: '+1 (821) 447-3782',
                        isActive: true,
                        age: 39,
                        company: 'POLARAX',
                    },
                    {
                        id: 1,
                        firstName: 'Caroline',
                        lastName: 'Jensen',
                        email: 'carolinejensen@zidant.com',
                        dob: '2004-05-28',
                        address: {
                            street: '529 Scholes Street',
                            city: 'Temperanceville',
                            zipcode: 5235,
                            geo: {
                                lat: 23.806115,
                                lng: 164.677197,
                            },
                        },
                        phone: '+1 (821) 447-3782',
                        isActive: true,
                        age: 39,
                        company: 'POLARAX',
                    },
                ],
                Edit: false,
                sessionList: [],
                pageInfo: 1,
                page: 1,
            };
        },
        created() {
            // this.getSessionsList();
        },
        watch: {
            page() {
                this.getSessionsList();
            },
        },
        methods: {
            goToHome() {
                this.addNew = false;
                this.Edit = false;
                this.getSessionsList();
            },
            handleCurrentChange(val) {
                this.page = val;
            },
            async getSessionsList() {
                await axios.get(`${this.liveUrl}/api/recording-session?page=${this.page}`).then((res) => {
                    this.sessionList = res.data.data;
                    this.pageInfo = res.data.meta;
                });
            },
            async deleteSession(id) {
                await axios
                    .delete(`${this.liveUrl}/api/recording-session/${id}`)
                    .then((res) => {
                        Notification.success({
                            title: '',
                            message: 'تم الحذف  بنجاح',
                            type: 'success',
                        });
                        this.getSessionsList();
                    })
                    .catch((err) => {
                        Notification.error({
                            title: '',
                            message: err.response.data.message,
                            type: 'error',
                        });
                        Object.keys(err.response.data.errors).forEach((key) => {
                            var errMSG = err.response.data.errors[key][0];
                            setTimeout(() => {
                                Notification.error({
                                    title: '',
                                    message: errMSG,
                                    type: 'error',
                                });
                            }, 500);
                        });
                    });
            },
            deleteModal(id, name) {
                this.$confirm(this.t('areYouSure'), name, {
                    confirmButtonText: 'OK',
                    cancelButtonText: 'Cancel',
                    type: 'error',
                    center: true,
                })
                    .then(() => {
                        this.deleteSession(id);
                    })
                    .catch(() => {});
            },
        },
    };
</script>
<style>
@import '@bhplugin/vue3-datatable/dist/style.css';

</style>