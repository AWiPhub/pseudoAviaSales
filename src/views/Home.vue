<template>
  <div class="home">
    <a-row type="flex" align="middle">
      <a-col class="steps">
        <a-steps direction="vertical" :current="current">
          <a-step title="Студент" />
          <a-step title="Информация о полёте" />
          <a-step title="Билеты " />
          <a-step title="Отель" />
          <a-step title="Клиент" />
          <a-step title="Детали" />
          <a-step title="Итог" />
        </a-steps>
      </a-col>

      <a-col>
        <template v-if="current == 0">
          <a-row> Информация о студенте </a-row>
          <br />
          <a-row style="width: 425px">
            Киланов Павел Сергеевич, студент 4 курса Югорского Государственного
            Университета, Институт Цифровой Экономики, направления “Информатика
            и Вычислительная Техника”, группа 1183б
          </a-row>
          <br />
          <a-row style="width: 425px">
            Вы работаете в туристической компании. Ваша компания работает с
            клиентами, продавая им путевки. Вашей задачей является отслеживание
            финансовой стороны деятельности фирмы
          </a-row>
          <a-row type="flex" justify="center" style="margin-top: 20px">
            <a-button type="primary" @click="next">Дальше</a-button>
          </a-row>
        </template>

        <template v-if="current == 1">
          <a-row>
            <a-col>
              <a-card title="Перелёт" style="width: 600px; margin-left: 10px">
                <a-form
                  :model="flyInTo"
                  :label-col="{ span: 6 }"
                  :wrapper-col="{ span: 16 }"
                >
                  <a-form-item label="Откуда">
                    <a-select
                      show-search
                      ref="select"
                      v-model:value="flyInTo.out"
                    >
                      <a-select-option
                        v-for="city of cities.out"
                        :key="city"
                        :value="city"
                        >{{ city }}</a-select-option
                      >
                    </a-select>
                  </a-form-item>
                  <a-form-item label="Куда">
                    <a-select
                      show-search
                      ref="select"
                      v-model:value="flyInTo.to"
                    >
                      <a-select-option
                        v-for="city of cities.to"
                        :key="city"
                        :value="city"
                        >{{ city }}</a-select-option
                      >
                    </a-select>
                  </a-form-item>
                  <a-form-item label="Дата вылета">
                    <a-date-picker
                      :allowClear="false"
                      v-model:value="flyInTo.departureDate"
                      :format="'DD.MM.YYYY'"
                      :disabled-date="disabledDate"
                      placeholder="Выберите дату"
                    />
                  </a-form-item>
                  <a-form-item label="Дата возвращения">
                    <a-date-picker
                      :allowClear="false"
                      v-model:value="flyInTo.returnDate"
                      :format="'DD.MM.YYYY'"
                      :disabled-date="disabledReturnDate"
                      placeholder="Выберите дату"
                    />
                  </a-form-item>
                </a-form>
              </a-card>
            </a-col>
            <a-col>
              <div class="options">
                <span style="font-size: 32px">Возможные варианты</span>
                <div
                  v-for="option of options"
                  :key="option.city"
                  style="
                    width: 400px;
                    background-color: #fff;
                    padding: 5px 15px 15px 15px;
                    margin-top: 10px;
                  "
                >
                  <a-row type="flex" align="middle">
                    <a-col style="font-size: 24px">
                      {{ option.city }}
                    </a-col>
                    <a-col style="font-size: 12px; margin-left: 10px">
                      {{ option.country }}
                    </a-col>
                  </a-row>
                  <a-row style="font-size: 16px">
                    <a-col> Погода:&emsp; </a-col>
                    <a-col>
                      {{ option.climat.temp }},
                      {{ option.climat.precipitation }},
                      {{ option.climat.wind }}
                    </a-col>
                  </a-row>
                  <a-row style="font-size: 16px"> Отели </a-row>
                  <a-row
                    type="flex"
                    justify="space-between"
                    v-for="hotel of option.hotels"
                    :key="hotel"
                  >
                    <a-col :span="12">
                      {{ hotel.name }}
                    </a-col>
                    <a-col :span="8"> от {{ hotel.priceDay }}₽/день </a-col>
                    <a-col :span="4"> {{ hotel.rate }}/10 </a-col>
                  </a-row>
                </div>
              </div>
            </a-col>
          </a-row>

          <a-row type="flex" justify="center" style="margin-top: 20px">
            <a-button @click="prev" style="margin-right: 5px">Назад</a-button>
            <a-button
              type="primary"
              @click="next"
              style="margin-left: 5px"
              :disabled="
                !(
                  flyInTo.out &&
                  flyInTo.to &&
                  flyInTo.departureDate &&
                  flyInTo.returnDate
                )
              "
            >
              Найти билеты
            </a-button>
          </a-row>
        </template>

        <template v-if="current == 2">
          <div class="ticketsList" :style="{ marginTop: '15px' }">
            <template v-if="foundedTickets.length == 0">
              <a-typography-title :level="3" :style="{ marginLeft: '25px' }">
                Билетов не найдено
              </a-typography-title>
            </template>
            <template v-else>
              <a-typography-title :level="3" :style="{ marginLeft: '25px' }">
                Найденные билеты
              </a-typography-title>
              <a-row
                v-for="ticket of foundedTickets"
                :key="ticket.id"
                :style="{
                  marginTop: '10px',
                  backgroundColor: '#fff',
                  padding: '10px 20px',
                  borderRadius: '10px',
                }"
                type="flex"
                justify="space-around"
                align="middle"
              >
                <a-col :style="{ width: '95px' }">
                  <a-row
                    :style="{
                      fontWeight: '600',
                      fontSize: '23px',
                      lineHeight: '30px',
                    }"
                  >
                    {{ ticket.price }} ₽
                  </a-row>
                </a-col>

                <a-col :style="{ marginLeft: '25px', width: '120px' }">
                  <a-row class="time">
                    {{ getTime(ticket.departureDate) }}
                  </a-row>
                  <a-row class="bottomTimeText">
                    {{ ticket.out }}
                  </a-row>
                  <a-row class="bottomTimeText">
                    {{ getDate(ticket.departureDate) }}
                  </a-row>
                </a-col>

                <a-col class="center" :style="{ width: '350px' }">
                  <a-row type="flex" justify="space-between">
                    <a-col :style="{ color: '#9ea9b7' }">
                      <svg
                        width="24"
                        height="24"
                        viewBox="0 0 24 24"
                        fill="currentColor"
                        xmlns="http://www.w3.org/2000/svg"
                        data-test-id="icon"
                        class="icon_cbe9250 segment-route__path-endpoint-icon"
                        aria-hidden="true"
                      >
                        <path
                          fill-rule="evenodd"
                          clip-rule="evenodd"
                          d="M16.954 9.793l-2.719 1.268c-2-.64-4.862-1.236-7.398-2.056l-1.201.55c1.32 1.029 3.135 2.33 4.52 3.408L7.439 14.23c-1.173-.248-2.418-.338-3.349-.532-.506-.106-.816.339-.338.71 2.256 1.759 3.159 2.469 3.159 2.469.392.305.875.497 1.343.283l12.445-5.803c.906-.46.871-1.106.64-1.629-.547-1.173-2.049-1.003-4.384.064z"
                        ></path>
                      </svg>
                    </a-col>
                    <a-col>
                      <span class="race"
                        >В пути:
                        {{
                          getDiff(ticket.departureDate, ticket.downDate)
                        }}</span
                      >
                    </a-col>
                    <a-col :style="{ color: '#9ea9b7' }">
                      <svg
                        width="24"
                        height="24"
                        viewBox="0 0 24 24"
                        fill="currentColor"
                        xmlns="http://www.w3.org/2000/svg"
                        data-test-id="icon"
                        class="icon_cbe9250 segment-route__path-endpoint-icon"
                        aria-hidden="true"
                      >
                        <path
                          fill-rule="evenodd"
                          clip-rule="evenodd"
                          d="M16.109 14.02l-2.719-1.268c-.795-1.945-2.178-4.52-3.18-6.99l-1.193-.567c.06 1.674.23 3.9.295 5.655l-2.72-1.268c-.564-1.058-1.295-2.069-1.744-2.907-.245-.456-.785-.408-.762.198.103 2.858.14 4.006.14 4.006.018.497.182.99.646 1.21l12.445 5.804c.935.398 1.407-.043 1.659-.557.547-1.173-.549-2.214-2.867-3.317z"
                        ></path></svg
                    ></a-col>
                  </a-row>
                  <a-row class="line"> </a-row>
                </a-col>

                <a-col :style="{ width: '120px' }">
                  <a-row class="time">
                    {{ getTime(ticket.downDate) }}
                  </a-row>
                  <a-row class="bottomTimeText">
                    {{ ticket.to }}
                  </a-row>
                  <a-row class="bottomTimeText">
                    {{ getDate(ticket.downDate) }}
                  </a-row>
                </a-col>

                <a-col>
                  <a-button @click="selectTicket(ticket)"> Выбрать </a-button>
                </a-col>
              </a-row>
            </template>
          </div>

          <a-row type="flex" justify="center" style="margin-top: 20px">
            <a-button @click="prev">Назад</a-button>
          </a-row>
        </template>

        <template v-if="current == 3">
          <div class="ticketsList" :style="{ marginTop: '15px' }">
            <template v-if="foundedHotels.length == 0">
              <a-typography-title :level="3">
                Отели не найдены
              </a-typography-title>
            </template>
            <template v-else>
              <a-typography-title :level="3">
                Найденные отели
              </a-typography-title>
              <a-row
                v-for="hotel of foundedHotels"
                :key="hotel"
                :style="{
                  marginTop: '10px',
                  backgroundColor: '#fff',
                  padding: '10px 20px',
                  borderRadius: '10px',
                  width: '900px',
                }"
                type="flex"
                justify="space-around"
                align="middle"
              >
                <a-col :span="9">
                  <a-row
                    :style="{
                      fontWeight: '600',
                      fontSize: '23px',
                      lineHeight: '30px',
                    }"
                  >
                    {{ hotel.name }}
                  </a-row>
                </a-col>

                <a-col :span="6">
                  <a-row
                    :style="{
                      fontWeight: '600',
                      fontSize: '23px',
                      lineHeight: '30px',
                    }"
                  >
                    {{ hotel.priceDay }} ₽/день
                  </a-row>
                </a-col>

                <a-col :span="4">
                  <a-row
                    :style="{
                      fontWeight: '600',
                      fontSize: '23px',
                      lineHeight: '30px',
                    }"
                  >
                    {{ hotel.rate }}/10
                  </a-row>
                </a-col>

                <a-col :span="3">
                  <a-button @click="selectHotel(hotel)"> Выбрать </a-button>
                </a-col>
              </a-row>
            </template>
          </div>

          <a-row type="flex" justify="center" style="margin-top: 20px">
            <a-button @click="prev" style="margin-right: 5px">Назад</a-button>
            <a-button @click="skip" style="margin-left: 5px"
              >Пропустить</a-button
            >
          </a-row>
        </template>

        <template v-if="current == 4">
          <a-card title="Информация о клиенте" style="width: 700px">
            <a-form
              :model="clientInfo"
              :label-col="{ span: 4 }"
              :wrapper-col="{ span: 18 }"
            >
              <a-form-item label="Фамилия">
                <a-input v-model:value="clientInfo.surname" />
              </a-form-item>
              <a-form-item label="Имя">
                <a-input v-model:value="clientInfo.name" />
              </a-form-item>
              <a-form-item label="Отчество">
                <a-input v-model:value="clientInfo.patronymic" />
              </a-form-item>
              <a-form-item label="Адрес">
                <a-textarea v-model:value="clientInfo.address" :rows="4" />
              </a-form-item>
              <a-form-item label="Телефон">
                <a-input
                  v-model:value="clientInfo.phone"
                  @change="phonechanger"
                />
              </a-form-item>
            </a-form>
          </a-card>

          <a-row type="flex" justify="center" style="margin-top: 20px">
            <a-button @click="prev" style="margin-right: 5px">Назад</a-button>
            <a-button
              type="primary"
              @click="next"
              style="margin-left: 5px"
              :disabled="
                !(
                  clientInfo.surname &&
                  clientInfo.name &&
                  clientInfo.patronymic &&
                  clientInfo.phone
                )
              "
            >
              Дальше
            </a-button>
          </a-row>
        </template>

        <template v-if="current == 5">
          <a-typography-title
            :level="3"
            style="text-align: center"
            :min="0"
            :max="100"
          >
            Детали заказа
          </a-typography-title>

          <a-row type="flex" justify="center">
            <a-form
              :label-col="{ style: { width: '125px' } }"
              :wrapper-col="{ span: 14 }"
            >
              <a-form-item label="Кол-во путёвок">
                <a-input-number v-model:value="kolvo" :min="1" />
              </a-form-item>
              <a-form-item label="Скидка">
                <a-input-number
                  v-model:value="discount"
                  :min="0"
                  :max="100"
                  :formatter="(value) => `${value}%`"
                  :parser="(value) => value.replace('%', '')"
                />
              </a-form-item>
            </a-form>
          </a-row>

          <a-row type="flex" justify="center" style="margin-top: 20px">
            <a-button @click="prev" style="margin-right: 5px">Назад</a-button>
            <a-button type="primary" @click="next" style="margin-left: 5px"
              >Дальше</a-button
            >
          </a-row>
        </template>

        <template v-if="current == 6">
          <a-typography-title
            :level="3"
            style="text-align: center"
            :min="0"
            :max="100"
          >
            Информация о заказе
          </a-typography-title>

          <a-descriptions bordered>
            <a-descriptions-item label="ФИО">
              {{ clientInfo.surname }} {{ clientInfo.name }}
              {{ clientInfo.patronymic }}
            </a-descriptions-item>
            <a-descriptions-item label="Телефон">
              {{ clientInfo.phone }}
            </a-descriptions-item>
            <a-descriptions-item label="Количество путёвок">
              {{ kolvo }}
            </a-descriptions-item>
            <a-descriptions-item label="Откуда">
              {{ flyInTo.out }}
            </a-descriptions-item>
            <a-descriptions-item label="Куда">
              {{ flyInTo.to }}
            </a-descriptions-item>
            <template v-if="Object.keys(selectedHotel).length > 0">
              <a-descriptions-item label="Отель">
                {{ selectedHotel.name }}
              </a-descriptions-item>
            </template>
            <a-descriptions-item v-else> </a-descriptions-item>
            <a-descriptions-item label="Дата отправления">
              {{ getDate(flyInTo.departureDate) }}
            </a-descriptions-item>
            <a-descriptions-item label="Дата возвращения">
              {{ getDate(flyInTo.returnDate) }}
            </a-descriptions-item>
            <a-descriptions-item> </a-descriptions-item>
            <a-descriptions-item label="Итоговая цена">
              {{ getPrice() }} ₽
            </a-descriptions-item>
          </a-descriptions>

          <a-row type="flex" justify="center" style="margin-top: 20px">
            <a-button @click="prev">Назад</a-button>
          </a-row>
        </template>
      </a-col>
    </a-row>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, reactive, ref, watch } from "vue";
import moment, { Moment } from "moment";
import ticketBase from "./ticktetsBASE.json";
import optionsBase from "./options.json";
import citiesBase from "./cities.json";

export default defineComponent({
  name: "Home",
  setup() {
    const options = ref(optionsBase);
    const current = ref(0);
    const cities = ref(citiesBase);

    const next = function () {
      if (current.value == 1) {
        search();
      }
      current.value = current.value + 1;
    };
    const prev = function () {
      current.value = current.value - 1;
    };

    const tickets = ref(ticketBase);
    const clientInfo = reactive({
      surname: "",
      name: "",
      patronymic: "",
      address: "",
      phone: "",
    });
    const flyInTo = reactive({
      out: "",
      to: "",
      departureDate: undefined,
      returnDate: undefined,
    });

    const getTime = function (date) {
      return moment(date).lang("ru").format("LT");
    };
    const getDate = function (date) {
      return moment(date).lang("ru").format("ll");
    };
    const getDiff = function (dateOut, dateTo) {
      const diff = moment(dateTo).diff(moment(dateOut));
      const duration = moment.duration(diff);
      const days = duration.days();
      const hours = duration.hours();
      const minutes = duration.minutes();
      const seconds = duration.seconds();
      return `${days ? days + "д" : ""} ${hours ? hours + "ч" : ""} ${
        minutes ? minutes + "м" : ""
      } ${seconds ? seconds + "с" : ""}`;
    };

    const foundedTickets = ref([]);

    const search = function () {
      foundedTickets.value = tickets.value.filter(
        (row) =>
          row.out === flyInTo.out &&
          row.to === flyInTo.to &&
          moment(row.departureDate).format("DD.MM.YYYY") ==
            flyInTo.departureDate.format("DD.MM.YYYY")
      );
    };

    const selectedTicket = reactive({});
    const selectedHotel = reactive({});
    const foundedHotels = ref([]);

    const searchHotels = function () {
      foundedHotels.value = options.value.find(
        (row) => row.city == flyInTo.to
      ).hotels;
    };

    const selectTicket = function (ticket) {
      Object.assign(selectedTicket, ticket);
      current.value++;
      searchHotels();
    };

    const selectHotel = function (hotel) {
      Object.assign(selectedHotel, hotel);
      current.value++;
    };

    const skip = function () {
      for (var member in selectedHotel) delete selectedHotel[member];
      current.value++;
    };

    const discount = ref(0);
    const kolvo = ref(1);

    const getPrice = function () {
      const diff = moment(flyInTo.returnDate).diff(
        moment(flyInTo.departureDate)
      );
      const duration = moment.duration(diff);
      const daysOfStay = duration.days();

      if (Object.keys(selectedHotel).length > 0) {
        return Math.round(
          (selectedTicket["price"] * 2 * kolvo.value +
            selectedHotel["priceDay"] * daysOfStay) *
            (1 - discount.value / 100)
        );
      } else {
        return Math.round(
          selectedTicket["price"] * 2 * kolvo.value * (1 - discount.value / 100)
        );
      }
    };

    watch(current, () => {
      if (current.value == 2) {
        foundedHotels.value = [];
      }
    });

    function randomDate(start, end) {
      return new Date(
        start.getTime() + Math.random() * (end.getTime() - start.getTime())
      );
    }

    const disabledDate = function (current: Moment) {
      let dates = [];
      tickets.value
        .filter((row) => row.out == flyInTo.out && row.to == flyInTo.to)
        .map((row) =>
          dates.push(moment(row.departureDate).format("DD.MM.YYYY"))
        );
      return !(current && dates.includes(current.format("DD.MM.YYYY")));
    };

    const disabledReturnDate = function (current: Moment) {
      return current && current < moment(flyInTo.departureDate).endOf("day");
    };

    const phonechanger = function () {
      const numbers = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];
      let result = "";
      const checkField = clientInfo.phone.split("");
      checkField.map((symbol, index) => {
        if (!numbers.includes(symbol)) {
          symbol = "";
        } else {
          result += symbol;
        }
      });
      if (result.length > 11) {
        result = result.slice(0, -1);
      }
      clientInfo.phone = result;
    };

    onMounted(() => {
      for (let index = 3; index < 200; index++) {
        const out =
          cities.value.out[Math.floor(Math.random() * cities.value.out.length)];
        const to =
          cities.value.to[Math.floor(Math.random() * cities.value.to.length)];
        const price = Math.floor(Math.random() * 11999) + 7000;
        const departureDate = randomDate(new Date(2022, 4, 10), new Date());
        const randomH = Math.floor(Math.random() * 4);
        const randomM = Math.floor(Math.random() * 60);
        const randomS = Math.floor(Math.random() * 60);
        const downDate = moment(departureDate)
          .add(randomH, "hours")
          .add(randomM, "minutes")
          .add(randomS, "seconds");

        tickets.value.push({
          id: index,
          out: out,
          to: to,
          price: price,
          departureDate: moment(departureDate).format(),
          downDate: moment(downDate).format(),
        });
      }
    });

    return {
      current,
      next,
      prev,
      clientInfo,
      flyInTo,
      tickets,
      getTime,
      getDate,
      getDiff,
      search,
      foundedTickets,
      options,
      selectTicket,
      foundedHotels,
      selectHotel,
      skip,
      discount,
      kolvo,
      selectedHotel,
      getPrice,
      cities,
      disabledDate,
      disabledReturnDate,
      phonechanger,
    };
  },
});
</script>

<style lang="scss">
.ticketsList {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.time {
  font-size: 1.75rem;
  line-height: 1em;
  margin-bottom: 5px;
  color: #0c131d;
}
.bottomTimeText {
  font-size: 0.75rem;
  line-height: 1.5em;
  color: #9ea9b7;
}
.line {
  width: 350px;
  background-color: #cdd4de;
  height: 5px;
  border-radius: 2.5px;
  margin-bottom: 10px;
}
.race {
  width: 100%;
  color: #9ea9b7;
  text-align: center;
  padding-top: 3px;
  margin-bottom: 5px;
}
.center {
  margin: 0 20px;
}
.steps {
  position: fixed;
  left: 100px;
}
.options {
  position: fixed;
  right: 50px;
}
</style>
