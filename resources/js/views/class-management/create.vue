<template>
    <v-app>
        <v-container>
            <BtnJudul text="Create Class" />

            <v-card
            class="border-edit"
            tile
            >
                <!-- <v-card-text class="text-center"> -->
                <v-card-text>
                    <v-container>
                        <v-form
                        ref="form"
                        v-model="valid"
                        :lazy-validation="lazy"
                        >
                            <input type="file" style="display:none; " id="foto_profile" ref="foto_profile" accept="image/*" @change="eventChange">
                            <label for="" align="left">New Class</label>

                            <v-text-field
                            v-model="name"
                            :rules="nameRules"
                            label="Name"
                            required
                            ></v-text-field>

                            <v-select
                                v-model="courses"
                                :items="item_courses"
                                :rules="[v => !!v || 'Item is required']"
                                label="Courses"
                                item-text="name"
                                item-value="id"
                                required
                               clearable
                                @input="eventChange"
                            >

                                <template slot="selection" slot-scope="data" >

                                    <v-list-item-avatar>
                                        <v-img :src="data.item.foto_courses"></v-img>
                                    </v-list-item-avatar>

                                    <v-list-item-content>
                                        {{ data.item.name}}
                                    </v-list-item-content>

                                </template>
                                <template slot="item" slot-scope="data" >

                                    <v-list-item-avatar>
                                        <v-img :src="data.item.foto_courses"></v-img>
                                    </v-list-item-avatar>

                                    <v-list-item-content>
                                        {{ data.item.name}}
                                    </v-list-item-content>

                                </template>
                            </v-select>

                            <v-select
                                v-model="trainer"
                                :items="item_trainers"
                                :rules="[v => !!v || 'Item is required']"
                                label="Trainer"
                                item-text="name"
                                item-value="id"
                                required
                               clearable

                            >

                                <template slot="selection" slot-scope="data" >

                                    <v-list-item-avatar>
                                        <v-img :src="data.item.foto_profile"></v-img>
                                    </v-list-item-avatar>

                                    <v-list-item-content>
                                        {{ data.item.name}}
                                    </v-list-item-content>

                                </template>
                                <template slot="item" slot-scope="data" >

                                    <v-list-item-avatar>
                                        <v-img :src="data.item.foto_profile"></v-img>
                                    </v-list-item-avatar>

                                    <v-list-item-content>
                                        {{ data.item.name}}
                                    </v-list-item-content>

                                </template>
                            </v-select>


                            <v-select
                                v-model="days"
                                :items="item_days"
                                attach
                                chips
                                label="Days"
                                item-text="description"
                                item-value="id"
                                multiple
                            ></v-select>


                            <v-row>

                                <v-col sm="12"
                                    md="6"
                                >
                                <v-menu
                                    ref="menu"
                                    v-model="menu2"
                                    :close-on-content-click="false"
                                    :nudge-right="40"

                                    transition="scale-transition"
                                    offset-y
                                    max-width="250px"
                                    min-width="250px"
                                >
                                    <template v-slot:activator="{ on }">
                                    <v-text-field
                                        v-model="time"
                                        label="Mulai"

                                        readonly
                                        v-on="on"
                                    ></v-text-field>
                                    </template>
                                    <v-time-picker
                                    v-if="menu2"
                                    v-model="time"
                                    full-width
                                    @click:minute="$refs.menu.save(time)"
                                    ></v-time-picker>
                                </v-menu>
                                </v-col>

                                <v-col sm="12"
                                    md="6"
                                >
                                <v-menu
                                    ref="menus"
                                    v-model="menu3"
                                    :close-on-content-click="false"
                                    :nudge-right="40"
                                    @click:minute="$refs.menus.save(time)"
                                    transition="scale-transition"
                                    offset-y
                                    max-width="250px"
                                    min-width="250px"
                                >
                                    <template v-slot:activator="{ on }">
                                    <v-text-field
                                        v-model="sampai"
                                        label="Selesai"
                                        @click:minute="$refs.menus.save(sampai)"
                                        readonly
                                        v-on="on"
                                    ></v-text-field>
                                    </template>
                                    <v-time-picker
                                    v-if="menu3"
                                    v-model="sampai"
                                    full-width
                                    @click:minute="$refs.menus.save(sampai)"
                                    ></v-time-picker>
                                </v-menu>
                                </v-col>


                            </v-row>

                             <v-text-field
                            v-model="max_student"

                            label="Max Students"
                            required
                            ></v-text-field>

                            <v-row>
                                <v-col
                                cols="12"
                                align="right"
                                >
                                <v-btn
                                    :disabled="!valid"
                                    color="success"
                                    tile
                                    @click="save()"
                                    :loading="loading"
                                    >
                                    Save
                                    </v-btn>
                                </v-col>
                            </v-row>
                        </v-form>
                    </v-container>

                </v-card-text>

                <v-card-actions class="">

                </v-card-actions>
            </v-card>
        </v-container>
    </v-app>

</template>
<script>
// import {mapActions} from 'vuex'
import ClassMixin from '../../mixins/ClassMixin'
export default {
    name: 'Class.create',
    data: () => ({
        imgurl: 'storage/default/icon_admin.jpg',
        foto:''

   }),

    mixins:[ClassMixin],
    methods:{
        async save(){
            this.loading = true
            let url = window.location.pathname
            url = url.split('/')
            url = "/" + url[1]
            let data = new FormData()
            data.append('name',this.name)
            data.append('id_courses' , this.courses)
            data.append('id_trainer' , this.trainer)
            data.append('days' , JSON.stringify(this.days))
            data.append('mulai' , this.time)
            data.append('sampai' , this.sampai)
            data.append('max_student' , this.max_student)
            await this.axios.post(url,data,this.config)
            .then((ress) => {

                this.setSnakbar({
                    status:true,
                    pesan:ress.data.message,
                    color:'success'
                })
                this.$router.go(-1)
            })
            .catch((err)=>{
                if (err.response.status == 400 ) {
                    this.setSnakbar({
                    color:'red',
                    status:true,
                    pesan:err.response.data.message,
                    })
                }else{
                    this.setSnakbar({
                    status:true,
                    color:'red',
                    pesan:"Terjadi Kesalahan",
                    })
                }

                console.log(err.response)
            })
            this.loading = false

        },

        eventChange(){
            let id_category = this.item_courses.find(x => x.id === this.courses)
            let data = this.trainers.filter(x => x.id_category === id_category.id_category)

            this.item_trainers = data

        }

    },

    created(){
        this.getData()
    }

}
</script>

<style lang="css" scoped>
/* .v-chip--select{
    background-color: #212121 !important;
    color: white !important;
} */
</style>
