<template>
    <div class="container main-block" style="display: flex;">
        <div class="col-3" style="max-width: 250px;min-width: 250px;overflow-y: scroll;max-height: 60vh;display: none;">
            <ul v-for="(item, index) in news" class="list-group" style="margin-bottom: 7px;">
                <li class="list-group-item" style="padding: 7px 11px;">{{ index }} -  {{ item.substring(0, 20) }}</li>
            </ul>
        </div>
        <div class="row" style="margin-left: 30px;">
            <div class="col-9">
                
                <div class="mb-3">
                    <!-- <span class="badge text-bg-success">Обобщение</span> -->
                    <div class="spinner-border text-success" role="status" id="load-circle">
                        <span class="visually-hidden">Loading...</span>
                    </div>
                    <div style="display: inline-block">Что передается в ChatGPT: <i>Предоставь мне главное из новости в 3-4 предложениях и выдели ключевые лица, игроков, компании и т.д. в новости</i></div>
                    <div for="textarea_home" class="form-label">Ввод:</div>
                    <textarea class="form-control" id="textarea_home" rows="10" :value="post_text" disabled></textarea>
                    <!-- <div>
                        <button type="button" class="f-z-16 btn btn-success btn-control" @click="add_text">Добавить новость</button>
                        <button type="button" class="f-z-16 btn btn-success btn-control" style="margin-left: 10px;" @click="clear">Очистить</button>
                    </div> -->
                </div>
                <div class="mb-3">
                    <label for="home_textarea2" class="form-label">Вывод:</label>
                    <textarea class="form-control" id="home_textarea2" rows="10" v-model="output"></textarea>
                    <div>
                        <button type="button" class="f-z-16 btn btn-success btn-control" :disabled="btn_is_disabled || logs['Обобщение']?.result" @click="prepare_data">Запуск</button>
                    </div>
                </div>
            </div>
            <button type="button" class="f-z-16 btn btn-info btn-help" data-bs-toggle="modal" data-bs-target="#exampleModal" @click="home_view_modal = true">
                Информация и пояснение
            </button>
            <div
                class="modal fade"
                id="exampleModal"
                tabindex="-1"
                aria-labelledby="exampleModalLabel"
                aria-hidden="true"
                :style="home_view_modal ? 'opacity: 1;overflow: visible;display: block;background-color: #0000006b;' : ''"
                @click="home_view_modal = false">
                <div class="modal-dialog" :style="home_view_modal?'top: 25%;':''" @click.stop>
                    <div class="modal-content">
                    <div class="modal-header">
                        <h1 class="modal-title fs-5" id="exampleModalLabel">Обощение</h1>
                        <!-- <button type="button" class="f-z-16 btn-close" data-bs-dismiss="modal" aria-label="Close"></button> -->
                    </div>
                    <div class="modal-body">
                        Обобщение - это инструмент, при помощи которого вы можете выделить главное из своих новостей, а также вывести ключевые лица из текста(Если это возможно). На вход для языковой модели GPT подается специальное выражение-запрос: <b>"Предоставь мне главное из новости в 3-4 предложениях и выдели ключевые лица, игроков, компании и т.д. в новости"</b> которое применяется для каждой введенной новости. <br>
                        Список новостей можно наблюдать на панели слева.
                        <br><br>
                        Поле "Ввод" предназначено для ввода текста новости. <br>
                        Кнопка "Добавить новость" добовляет текст новости в список.<br>
                        Кнопка "Очистить" очищает список новостей и поле ввода. <br>
                        Кнопка "Запуск" отправит ваши новости в GPT и сгенерирует ответ
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="f-z-16 btn btn-secondary" data-bs-dismiss="modal" @click="home_view_modal = false">Close</button>
                    </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    
    import { Configuration, OpenAIApi } from "openai"
    import { post_text, logs } from '../use/index'
    import axios from 'axios'
    const configuration = new Configuration({
        apiKey: "KEY",
    });
    let prepared_data = ""
    const openai = new OpenAIApi(configuration);

    export default{
        data() {
            return {
                news: [],
                home_view_modal: false,
                btn_is_disabled: false,
                output: '',
            }
        },
        setup() {
            return {
                post_text,
                logs
            }
        },
        watch: {
            logs(newValue) {
                this.output = newValue["Обобщение"]?.result ?? ''
            }
        },
        mounted() {
            this.output = this.logs["Обобщение"]?.result ?? ''
        },
        methods: {
            clear() {
                const textArea = document.getElementById("textarea_home")
                textArea.value = ""
                // this.news = []
            },
            // add_text() {
            //     const textArea = document.getElementById("textarea_home")
            //     let temp = textArea.value
            //     if (temp == "") {
            //         alert("Внимание! Вы не ввели текст!")
            //         return 0
            //     }
            //     this.news.push(temp)
            //     textArea.value = ""
            // },
            async prepare_data() {
                if (!this.post_text) {
                    // alert("Внимание! Вы не добавили новости!")
                    alert("Внимание! Текст новости пустой!")
                    return 0
                }
                prepared_data = "\n"
                const load_elem = document.getElementById("load-circle")
                load_elem.style.display = "inline-block"
                this.btn_is_disabled = true







                axios.get(`/ru/gpt-service/get-log?news_id=${document.getElementById('item_id')?.value}&news_type=${document.getElementById('r_type')?.value}&promt=Обобщение`)
                    .then(async (response) => {
                        console.log('gpt-service - response = ', response?.data);
                        if (response?.data == false) {

                            const completion = await openai.createChatCompletion({
                                model: "gpt-3.5-turbo",
                                messages: [
                                    {'role': 'system', 'content': 'You are an assistant for the monitoring system. You must highlight the main thing from the provided news and state it in 3-4 sentences and highlight key persons, players, companies, etc. from the news. Output format - SUMMARY: , KEY_PERSONS_AND_COMPANYS: . Output make on russion language'},
                                    {'role': 'user', 'content': 'Предоставь мне главное из новости в 3-4 предложениях и выдели ключевые лица, игроков, компании и т.д. из новости'},
                                    {'role': 'user', 'content': 'Представляемая новость: ' + this.post_text}
                                ],
                            });
                            console.log(completion.data.choices[0].message.content)
                            prepared_data = prepared_data + "\n\n" + completion.data.choices[0].message.content + "\n\n================================"

                            load_elem.style.display = "none"
                            this.btn_is_disabled = false
                            // const textArea2 = document.getElementById("home_textarea2")
                            this.output = prepared_data

                            if (!this.output) return

                            let formData = new FormData();

                            formData.append('news_id', document.getElementById('item_id')?.value)
                            formData.append('news_type', document.getElementById('r_type')?.value)
                            formData.append('promt', 'Обобщение')
                            formData.append('result', this.output)

                            axios.post(`/ru/gpt-service/create-log`, formData)
                                .then((response) => {
                                    console.log('gpt-service - create-log response = ', response);
                                })
                                .catch(error => {
                                    console.log('error', error);
                                })
                        }
                        else {
                            load_elem.style.display = "none"
                            this.btn_is_disabled = false
                            this.output = response?.data?.result
                        }
                    })
                    .catch(error => {
                        console.log('error', error);
                    })
            }
        },
    }
</script>

<style>
.btn-help {
    margin: 0 auto;
    margin-top: 30px;
}
#load-circle {
    display: none;
}
.btn-control {
    margin-top: 20px;
}
</style>