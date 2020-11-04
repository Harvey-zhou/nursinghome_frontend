<template>
    <div id="outgoing_panel">
        <div id="outgoing_form">
            <el-form ref="outGoingForm" :model="outGoingForm" label-width="80px">
                <el-form-item label="客人姓名">
                    <el-input v-model="outGoing.customerName"></el-input>
                </el-form-item>

                <el-form-item label="外出日期">
                    <el-input v-model="outGoing.outgoingDate"></el-input>
                </el-form-item>

                <el-form-item label="外出原因">
                    <el-input v-model="outGoing.outgoingReason"></el-input>
                </el-form-item>

                <el-form-item label="返回日期">
                    <el-input v-model="outGoing.returnDate"></el-input>
                </el-form-item>

                <el-form-item label="备注">
                    <el-input type="textarea" v-model="outGoing.remarks"></el-input>
                </el-form-item>


                <el-form-item>
                    <el-button type="primary" @click="submitOutGoing">
                        提交
                    </el-button>
                    <el-button @click="clearAll">
                        查看
                    </el-button>
                </el-form-item>
            </el-form>
        </div>

        <div id="outgoing_table">
            <el-table :data="pagedOutGoing" style="width: 100%;">
                <el-table-column
                        label="客人姓名"
                        width="180">
                    <template slot-scope="scope">
                        <span style="margin-left: 10px">{{ scope.row.customerName }}</span>
                    </template>
                </el-table-column>

                <el-table-column
                        label="外出日期"
                        width="180">
                    <template slot-scope="scope">
                        <i class="el-icon-time"></i>
                        <span style="margin-left: 10px">{{ scope.row.outgoingDate }}</span>
                    </template>
                </el-table-column>

                <el-table-column
                        label="外出原因"
                        width="180">
                    <template slot-scope="scope">
                        <span style="margin-left: 10px">{{ scope.row.outgoingReason }}</span>
                    </template>
                </el-table-column>

                <el-table-column
                        label="返回日期"
                        width="180">
                    <template slot-scope="scope">
                        <i class="el-icon-time"></i>
                        <span style="margin-left: 10px">{{ scope.row.returnDate }}</span>
                    </template>
                </el-table-column>

                <el-table-column
                        label="备注"
                        width="180">
                    <template slot-scope="scope">
                        <span style="margin-left: 10px">{{ scope.row.remarks }}</span>
                    </template>
                </el-table-column>

                <el-table-column label="操作">
                    <template slot-scope="scope">
                        <el-button
                                size="mini"
                                type="danger"
                                @click="handleDelete(scope.$index, scope.row)">删除
                        </el-button>
                    </template>
                </el-table-column>
            </el-table>
        </div>
    </div>
</template>

<script>
    import moment from "moment";

    export default {
        name: "OutGoing",
        data() {
            return {
                outGoing: {
                    customerName: "",
                    outgoingDate: "",
                    outgoingReason: "",
                    returnDate: "",
                    remarks: ""
                },
                pagedOutGoing: {}
            };
        },
        methods: {
            async submitOutGoing() {
                let url = "http://localhost:8081/outgoing/add?";
                this.outGoing.outgoingDate = moment().format('YYYY/MM/DD');
                // let urlWithParam = await this.concoctParams(url);
                for (let key in this.outGoing) {
                    console.log(key + " " + this.outGoing[key]);
                }
                this.$ajax.post(url, this.outGoing).then(res => {
                        console.log(res.data);
                        if (res.data) {
                            this.$message.success("入住信息成功录入！");
                        } else {
                            this.$message.error("入住信息录入失败，请稍后再试！");
                        }
                    }
                ).catch(exception => {
                    console.error(exception);
                });
            },

            async clearAll() {
                this.outGoing.customerName = "";
                this.outGoing.outgoingDate = "";
                this.outGoing.outgoingReason = "";
                this.outGoing.returnDate = "";
                this.outGoing.remarks = "";

                await this.getAllOutGoingRecords();
            },


            async getAllOutGoingRecords() {
                let url = "http://localhost:8081/outgoing/findAll";
                this.$ajax.get(url).then(resp => {
                    this.pagedOutGoing = resp.data;
                }).catch(error => {
                    console.log(error);
                });
            },
            // async getPagedCheckInEntries() {
            //     let url = `http://localhost:8081/checkin/paged_checkin_entries?pageNum=${this.pageParams.pageNum}
            //       &pageSize=${this.pageParams.pageSize}`;
            //     console.log(url);
            //     this.$ajax.get(url).then(resp => {
            //         if (!resp) {
            //             this.$message.error("拉取入住信息错误，请稍后再试！");
            //             return;
            //         }
            //         this.pagedCheckInEntries = resp.data;
            //     }).catch(error => {
            //         console.log(error)
            //     })
            // },

            async handleDelete(index, row) {
                let entryId = row.id;
                console.log("delete: " + row.id);
                let url = `http://localhost:8081/outgoing/delete/${entryId}`;
                this.$ajax.delete(url).then(resp => {
                    if (resp.data) {
                        this.$message.success("删除条目成功！");
                    } else {
                        this.$message.error("删除条目失败，请稍后再试！");
                    }
                }).catch(error => {
                    console.log(error);
                })
                // // 重新拉取数据
                // await this.getPagedCheckInEntries();
                // location.reload();
            }
        }
    }
</script>

<style scoped>

</style>
