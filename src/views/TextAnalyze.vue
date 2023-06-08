<template>
    <div class="container main-block" @click.stop="dropdown_menu = false;type_dropdown_menu = false">
        <div class="row">
            <div class="col-12">
                <div class="mb-3">
                    <!-- <span class="badge text-bg-success">Анализ новости</span> -->
                    <div class="dropdown dropdown_inline" style="display: flex;">
                        <button class="f-z-16 btn btn-success dropdown-toggle" type="button" data-bs-toggle="dropdown" aria-expanded="false" @click.stop="type_dropdown_menu = !type_dropdown_menu">
                            Тип
                        </button>
                        <ul class="dropdown-menu" :class="{
                            show: type_dropdown_menu
                        }">
                            <li><button class="dropdown-item" @click="type_dropdown_menu=false;condition_type = true;">Анализ</button></li>
                            <li><button class="dropdown-item" @click="type_dropdown_menu=false;condition_type = false;">Реакция</button></li>
                        </ul>
                        <button class="f-z-16 btn btn-success dropdown-toggle" type="button" :style="input != '' || !condition_type ? 'background: #ccc;border: 1px solid #ccc;pointer-events: none;' : ''" data-bs-toggle="dropdown" aria-expanded="false" @click.stop="dropdown_menu = !dropdown_menu" style="margin-left: 10px;">
                            Условие
                        </button>
                        <ul class="dropdown-menu" :class="{
                            show: dropdown_menu
                        }">
                            <li><button class="dropdown-item" @click="dropdown_menu=false;change_condition('бизнес')">Бизнес</button></li>
                            <li><button class="dropdown-item" @click="dropdown_menu=false;change_condition('государство')">Государство</button></li>
                            <li><button class="dropdown-item" @click="dropdown_menu=false;change_condition('сил.структуры')">Сил.Структуры</button></li>
                            <li><button class="dropdown-item" @click="dropdown_menu=false;change_condition('разные т.з.')">Разные т.з.</button></li>
                        </ul>
                        <input class="form-control" type="text" style="width: 350px;margin-left: 10px;font-size: 15px !important;" :placeholder="condition_type? 'Введите своё условие (...?)' : 'Напиши ответ от лица правительства'" :style="!condition_type && input == '' ? 'border-color: red !important;' : ''" v-model="input"/>
                        <span v-show="!condition_type && input == ''" style="
                            display: flex;
                            align-items: center;
                            margin-left: 5px;
                            color: red;
                            font-size: 15px;
                        ">От чьего лица вы ожидаете ответ?</span>
                        <!-- От лица кого вы ожидаете ответ? -->
                    </div>
                    <div class="cond">Что передается в ChatGPT: <i>{{ condition_type ? 'Предоставь мне подробный анализ данной новости.' : 'Проанализируй данную новость и сформируй ответ на ее основе.' }} {{ input == '' ? condition : input }}</i></div>
                    <div class="spinner-border text-success" role="status" id="load-circle-analyze">
                        <span class="visually-hidden">Loading...</span>
                    </div>
                    <div for="textarea_analyze" class="form-label">Ввод:</div>
                    <textarea class="form-control" id="textarea_analyze" rows="10" :value="post_text" disabled></textarea>
                    <!-- <div>
                        <button
                            type="button"
                            class="f-z-16 btn btn-success btn-control"
                            :style="input != '' ? 'background: #ccc;border:1px solid #ccc;pointer-events:none' : ''"
                            @click="push_news">Запуск</button>
                        <button type="button" class="f-z-16 btn btn-success btn-control" style="margin-left: 10px;" @click="clear">Очистить</button>
                    </div> -->
                </div>
                <div class="mb-3">
                    <label for="textarea2" class="form-label">Вывод:</label>
                    <textarea class="form-control" id="textarea2" rows="10" v-model="output"></textarea>

                    <div>
                        <button
                            type="button"
                            class="f-z-16 btn btn-success btn-control"
                            :style="!condition_type && input == '' ? 'background: #ccc;border:1px solid #ccc;pointer-events:none' : ''"
                            @click="push_news"
                            :disabled="btn_is_disabled">Запуск</button>
                    </div>
                </div>
                <button type="button" class="f-z-16 btn btn-info btn-help" data-bs-toggle="modal" data-bs-target="#exampleModal" @click="text_analyze_modal = true">
                    Информация и пояснение
                </button>
                <div
                    class="modal fade"
                    id="exampleModal"
                    tabindex="-1"
                    aria-labelledby="exampleModalLabel"
                    aria-hidden="true"
                    :style="text_analyze_modal ? 'opacity: 1;overflow: visible;display: block;background-color: #0000006b;' : ''"
                    @click="text_analyze_modal = false">
                    <div class="modal-dialog" :style="text_analyze_modal?'top: 25%;':''" @click.stop>
                        <div class="modal-content">
                        <div class="modal-header">
                            <h1 class="modal-title fs-5" id="exampleModalLabel">Анализ новости</h1>
                            <!-- <button type="button" class="f-z-16 btn-close" data-bs-dismiss="modal" aria-label="Close"></button> -->
                        </div>
                        <div class="modal-body">
                            Анализ новости - это инструмент, при помощи которого Вы можете получить подробный анализ новости от Исcкусственного Интелекта с той или иной точки зрения. На вход подается выражение - запрос: <b>"{{ condition_type ? 'Предоставь мне подробный анализ данной новости' : 'Проанализируй данную новость и сформируй ответ на ее основе' }} + точка зрения"</b> Во вкладке <b>"Условие"</b> можно выбрать точку зрения.
                            <br><br>
                            "Бизнес" - с точки зрения бизнеса.<br>
                            "Государство" - с точки зрения государства.<br>
                            "Сил.Структуры" - с точки зрения силовых структур<br>
                            "Разные т.з." - с разных точек зрения
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="f-z-16 btn btn-secondary" data-bs-dismiss="modal" @click="text_analyze_modal = false">Close</button>
                        </div>
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
        apiKey: "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",
    });
    let prepared_data = ""
    const openai = new OpenAIApi(configuration);
    export default{
        data() {
            return {
                condition: "",
                condition_type: true,
                input: "",
                output: "",
                text_analyze_modal: false,
                dropdown_menu: false,
                type_dropdown_menu: false,
                btn_is_disabled: false,
            }
        },
        setup() {
            return {
                post_text,
                logs
            }
        },
        methods: {
            clear() {
                const textArea = document.getElementById("textarea_analyze")
                textArea.value = ""
            },
            change_condition(statement) {
                if (statement == "бизнес") {this.condition = "С точки зрения бизнеса"} 
                if (statement == "государство") {this.condition = "С точки зрения государства"} 
                if (statement == "сил.структуры") {this.condition = "С точки зрения силовых структур"} 
                if (statement == "разные т.з.") {this.condition = "C разных точек зрения"} 

            },
            async push_news() {
                if (this.input.length > 0) {
                    this.condition = this.input
                    console.log(this.input)
                }

                if (this.condition == "") {
                    alert("Внимание! Вы не выбрали условие!")
                    return 0
                }
                const textArea = document.getElementById("textarea_analyze")
                let temp = textArea.value

                if (temp == "") {
                    alert("Внимание! Вы не ввели текст!")
                    return 0
                }

                if (this.input.length > 0) {
                    this.condition = this.input
                }

                const load_elem = document.getElementById("load-circle-analyze")
                load_elem.style.display = "inline-block"
                this.btn_is_disabled = true
                // const textArea2 = document.getElementById("textarea2")


                axios.get(`/ru/gpt-service/get-log?news_id=${document.getElementById('item_id')?.value}&news_type=${document.getElementById('r_type')?.value}&promt=${this.condition}&type=${this.condition_type ? 'analyze' : 'reaction'}`)
                    .then(async (response) => {
                        console.log('gpt-service - response = ', response?.data);
                        if (response?.data == false) {
                            console.log('condition_type', this.condition_type);
                            const completion = await openai.createChatCompletion({
                                model: "gpt-3.5-turbo",
                                messages: this.condition_type
                                    ? [
                                        {'role': 'system', 'content': 'You are an assistant for the monitoring system. You must give your own analysis of the presented news.'},
                                        {'role': 'user', 'content': 'Предоставь мне подробный анализ данной новости. ' + this.condition},
                                        {'role': 'user', 'content': 'Представленная новость: ' + temp}
                                    ]
                                    : [
                                        {'role': 'system', 'content': 'You are an assistant for the monitoring system. You must analyze the given news and issue an answer based on the given news according to the request'},
                                        {'role': 'user', 'content': 'Проанализируй данную новость и сформируй ответ на ее основе. ' + this.condition},
                                        {'role': 'user', 'content': 'Представленная новость: ' + temp}
                                    ]
                            });
                            this.output = completion.data.choices[0].message.content
                            load_elem.style.display = "none"
                            this.btn_is_disabled = false

                            if (!this.output) return

                            let formData = new FormData();

                            formData.append('news_id', document.getElementById('item_id')?.value)
                            formData.append('news_type', document.getElementById('r_type')?.value)
                            formData.append('type', this.condition_type ? 'analyze' : 'reaction')
                            formData.append('promt', this.condition)
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
                            this.output = response?.data?.result
                            load_elem.style.display = "none"
                            this.btn_is_disabled = false
                        }
                    })
                    .catch(error => {
                        console.log('error', error);
                    })

            },
        },
        watch: {
            logs(newValue) {
                const log_keys = Object.keys(newValue);
                if (log_keys.length == 0) return;
                // if (log_keys.length == 1 && log_keys[0] == "Обобщение") return;

                const temp_log_key = log_keys.find(prompt => (
                        prompt == "С точки зрения бизнеса" ||
                        prompt == "С точки зрения государства" ||
                        prompt == "С точки зрения силовых структур" ||
                        prompt == "C разных точек зрения"
                    ) && prompt != 'Обобщение')

                if (temp_log_key) {
                    this.condition = temp_log_key
                }

                if (this.condition == '') {
                    const log_key = log_keys.find(prompt => (
                        prompt != "С точки зрения бизнеса" &&
                        prompt != "С точки зрения государства" &&
                        prompt != "С точки зрения силовых структур" &&
                        prompt != "C разных точек зрения"
                    ) && prompt != 'Обобщение')
                    if (log_key) this.input = log_key
                }

                this.output = newValue[this.input != '' ? this.input : this.condition]?.result
            },
            condition(newValue) {
                const log_keys = Object.keys(this.logs);
                if (log_keys.length == 0) return;
                let temp_result = this.logs[this.input != '' ? this.input : newValue]?.result;
                this.output = temp_result || ''
            },
            input(newValue) {
                const log_keys = Object.keys(this.logs);
                if (log_keys.length == 0) return;
                let temp_result = this.logs[newValue != '' ? newValue : this.condition]?.result;
                this.output = temp_result || ''
            },
        }
    }
</script>

<style>
.btn-help {
    margin: 0 auto;
    margin-top: 30px;
}
.cond {
    display: inline-block;
    margin-top: 5px;
}
.dropdown_inline {
    display: inline-block;
}
#load-circle-analyze {
    display: none;
}
/* .main-block {
    margin-top: 50px;
} */
.btn-control {
    margin-top: 20px;
}
</style>