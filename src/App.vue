<template>
  <div class="calc-container py-5">
    <div class="container-md">
      <div class="card px-5 py-3 main-card">

        <h1 class="mb-4" style="text-align: center">Калькулятор оценки товара</h1>
        <div class="input-group mb-3 px-4">
          <input
              type="text"
              class="form-control border-dark"
              placeholder="Введите SKU или ссылку из Wildberries"
              aria-label="SKU или ссылка из Wildberries"
              v-model="sku"
          >
          <button
              class="btn btn-primary bg-dark border-dark count-btn"
              type="button"
              id="button-addon2"
              @click.stop="countFeedback"
          >
            Рассчитать
          </button>
        </div>

        <div
            class="container card-rating"
            v-show="isLoaded"
        >
          <h3 class="mt-3 mb-4" style="text-align: center">
            Рейтинг карточки товара Wildberries
          </h3>
          <div class="board">

            <div class="board-left">

              <div class="card mt-3 shadow-lg p-2">
                <img :src="skuObj.imgUrl" class="card-img-top p-2" :alt="skuObj.name">
                <div class="card-body">
                  <h5 class="card-title">{{ skuObj.brand }}</h5>
                  <p class="card-text">{{ skuObj.name }}</p>
                  <a target="_blank"
                     :href="`https://www.wildberries.ru/catalog/${skuObj.id}/detail.aspx?targetUrl=MI`"
                     class="btn btn-outline-dark border-dark"
                  >
                    Посмотреть на сайте
                  </a>
                </div>
              </div>

              <div class="card mt-3 rating px-3 py-2 mb-3">
                <div class="stars-container">
                  <span class="stars">5 ☆☆☆☆☆</span>
                  <span>{{ feedbackCount['1'] }} отз.</span>
                </div>
                <div class="stars-container">
                  <span class="stars">4 ☆☆☆☆</span>
                  <span>{{ feedbackCount['2'] }} отз.</span>
                </div>
                <div class="stars-container">
                  <span class="stars">3 ☆☆☆</span>
                  <span>{{ feedbackCount['3'] }} отз.</span>
                </div>
                <div class="stars-container">
                  <span class="stars">2 ☆☆</span>
                  <span>{{ feedbackCount['4'] }} отз.</span>
                </div>
                <div class="stars-container">
                  <span class="stars">1 ☆</span>
                  <span>{{ feedbackCount['5'] }} отз.</span>
                </div>
              </div>

            </div>

            <div class="board-right">

              <table class="table border-dark">
                <thead>
                <tr>
                  <th scope="col">Желаемый рейтинг</th>
                  <th scope="col">Сколько отзывов с 5 звёздами требуется получить</th>
                </tr>
                </thead>
                <tbody>
                <tr
                    v-for="(step, stepIdx) in skuObj.stepsToFive"
                    :key="stepIdx"
                >
                  <td>{{ step.stars }}</td>
                  <td>{{ step.feedbackRequired }}</td>
                </tr>
                </tbody>
              </table>

            </div>

          </div>
        </div>


      </div>


    </div>

  </div>

</template>

<script setup>
import { computed, reactive, ref } from 'vue';
import axios from 'axios';

const sku = ref('');
const skuObj = reactive({});
const feedbackCount = computed(() => {
  if (!Object.keys(skuObj).length) return {};
  return {
    1: Math.round(skuObj.valuationDistribution['1'] / 100 * skuObj.feedbackCount),
    2: Math.round(skuObj.valuationDistribution['2'] / 100 * skuObj.feedbackCount),
    3: Math.round(skuObj.valuationDistribution['3'] / 100 * skuObj.feedbackCount),
    4: Math.round(skuObj.valuationDistribution['4'] / 100 * skuObj.feedbackCount),
    5: Math.round(skuObj.valuationDistribution['5'] / 100 * skuObj.feedbackCount),
  };
});

const isLoaded = computed(() => Object.keys(skuObj).length);

const countFeedback = async () => {
  const url = `https://wildberries.api.org.ru/parse/${sku.value}`;
  const { data } = await axios.get(url);
  Object.assign(skuObj, data);
};
</script>

<style lang="scss" scoped>
.calc-container {
  margin: 0 auto;
  padding: 0;
  width: 100%;
  height: 100%;
  min-height: 100vh;
  background: url("https://i.imgur.com/Qxmb0jT.jpg") no-repeat center center fixed;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  background-size: cover;
}

.board {
  display: flex;
  flex-direction: row;
  justify-content: space-around;

  .board-left {
    width: 30%;
  }

  .board-right {
    width: 60%;
  }
}

.main-card {
  background-color: rgba(248, 246, 242, 0.76);
}

.form-control:focus {
  box-shadow: 0 0 0 0.2rem rgba(80, 87, 31, 0.15);
}

.card.rating {
  display: flex;
  flex-direction: column;
}

.stars {
  width: 5rem;
}

.stars-container {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
</style>
