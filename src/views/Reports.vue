<template>
    <div class="container">
        <div class="row">
            <div class="table-responsive">
                <h3>Reports</h3>
                <table class="table table-striped table-sm">
                    <thead>
                        <th>Reference #</th>
                        <th>Username</th>
                        <th>Subject</th>
                        <th>Content</th>
                        <th>Time-Sent</th>
                        <th>Date-Sent</th>
                        <th>Status</th>
                        <th>Actions</th>

                    </thead>
                    <tbody>
                        <tr v-for="report in reportList" :key="report.key">
                            <td>{{ report.id }}</td>
                            <td>{{ report.username }}</td>
                            <td v-if="report.subject != null">{{ report.subject }}</td>
                            <td v-else>{{ report.subjectTxt }}</td>
                            <td v-if="report.content != null">{{ report.content }}</td>
                            <td v-else>{{ report.messageTxt }}</td>
                            <td>{{ report.time_sent }}</td>
                            <td>{{ report.date_sent }}</td>
                            <td v-if="report.status == 'done'">{{report.status}}</td>
                            <td v-else>ongoing</td>
                            <td>
                                <button class="btn message"
                                    @click.prevent="respondUser(report.username)">Respond</button>
                            </td>
                            <td>
                                <button class="btn delete" @click.prevent="isFinished(report.id)">Done</button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        <!--row-->
    </div>
</template>

<script>
/* eslint-disable */
import { getDatabase, ref, get, child, update, onValue, remove } from "firebase/database";
export default {
    data() {
        return {
            reportList: [],
            isResolveList: [],
        }
    },
    mounted() {
        const db = getDatabase();

        //get all reports
        let viewReports = this;
        let viewResolve = this;
        const adbRef = ref(db, '/reports/');
        onValue(adbRef, (snapshot) => {
            let data = snapshot.val();
            let reportList = [];   
            Object.keys(data).forEach((key) => {
                reportList.push({
                    id: key,
                    content: data[key].content,
                    date_sent: data[key].date_sent,
                    subject: data[key].subject,
                    time_sent: data[key].time_sent,
                    username: data[key].username,
                    messageTxt: data[key].messageTxt,
                    subjectTxt: data[key].subjectTxt,
                    status : data[key].status,
                });

            });
            viewReports.reportList = reportList;
        });

    },
    methods: {
        respondUser(id) {
            this.$router.push({ name: 'chat-box', params: { id: id } })
        },
        isFinished(id) {
            const db = getDatabase();
            update(ref(db, '/reports/' + '/' + id), {
                status: 'done'
            }).then(() => {
                console.log("update success: " + id)
            })
                .catch((error) => {
                    console.log("update failed: " + id)
                });
        }
    }
}
</script>

<style scoped>
.row {
    top: 0;
    bottom: 0;
    right: 0;
    margin-left: 15%;
    margin-right: 5%;
    margin-bottom: 5%;
}

h3 {
    text-align: left;
}

.table {
    width: 100%;
}

.btn {
    border-radius: 10%;
}

.btn:hover {
    background-color: rgb(130, 130, 128);
}
</style>