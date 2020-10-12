<template>
  <b-container fluid="xl">
    <b-jumbotron
      id="search"
      header="Каталог компаний России"
      :lead="`Более ${titleCompaniesCount} фирм доступно для поиска`"
    >
      <p>
        Город, сфера деятельности, телефон, email, и многое другое в карточках компаний. Все данные доступны по
        <b-link href="https://api.leaq.ru/docs" target="_blank">
          API
        </b-link>
        для интеграции с вашим бизнесом
      </p>
    </b-jumbotron>
    <b-card
      border-variant="primary"
      class="mb-4"
    >
      <b-row>
        <b-col md="6">
          <b-form-group label="Город">
            <b-form-tag
              v-for="tag in city.tags"
              :key="tag.id"
              :title="tag"
              pill
              variant="primary"
              class="mr-1 mb-1"
              @remove="city.removeTag(tag)"
            >
              {{ tag.title }}
            </b-form-tag>
            <b-form-text v-if="city.tags.length === 0">
              Все города
            </b-form-text>
          </b-form-group>
        </b-col>

        <b-col md="6">
          <b-form-group label="Категория">
            <b-form-tag
              v-for="tag in category.tags"
              :key="tag.id"
              :title="tag"
              pill
              variant="primary"
              class="mr-1 mb-1"
              @remove="category.removeTag(tag)"
            >
              {{ tag.title }}
            </b-form-tag>
            <b-form-text v-if="category.tags.length === 0">
              Все категории
            </b-form-text>
          </b-form-group>
        </b-col>
      </b-row>
    </b-card>

    <b-row>
      <b-col md="6">
        <b-card
          class="mb-4"
          header="Наличие контактов"
        >
          <b-form-group>
            <b-row>
              <b-col md="6">
                <b-form-group label="Email">
                  <b-form-select
                    v-model="query.hasEmail"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>

              <b-col md="6">
                <b-form-group label="Телефон">
                  <b-form-select
                    v-model="query.hasPhone"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>
            </b-row>
          </b-form-group>
        </b-card>

        <b-card
          header="Наличие приложений"
          class="mb-4"
        >
          <b-form-group>
            <b-row>
              <b-col md="6">
                <b-form-group label="App Store">
                  <b-form-select
                    v-model="query.hasAppStore"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>

              <b-col md="6">
                <b-form-group label="Google Play">
                  <b-form-select
                    v-model="query.hasGooglePlay"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>
            </b-row>
          </b-form-group>
        </b-card>

        <b-card
          header="Наличие реквизитов"
          class="mb-4"
        >
          <b-form-group>
            <b-row>
              <b-col md="4">
                <b-form-group label="ИНН">
                  <b-form-select
                    v-model="query.hasInn"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>

              <b-col md="4">
                <b-form-group label="КПП">
                  <b-form-select
                    v-model="query.hasKpp"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>

              <b-col md="4">
                <b-form-group label="ОГРН">
                  <b-form-select
                    v-model="query.hasOgrn"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>
            </b-row>
          </b-form-group>
        </b-card>
      </b-col>

      <b-col md="6">
        <b-card
          header="Наличие соцсетей"
          class="mb-4"
        >
          <b-form-group>
            <b-row>
              <b-col md="6">
                <b-form-group label="VK">
                  <b-form-select
                    v-model="query.hasVk"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>

              <b-col md="6">
                <b-form-group label="VK подписчиков">
                  <b-row>
                    <b-col md="6">
                      <b-form-input
                        v-model="query['vkMembersCount.from']"
                        :disabled="query.hasVk !== 'YES'"
                        type="number"
                        placeholder="От"
                        min="0"
                        step="1"
                        :class="query['vkMembersCount.from'] === '' ? '' : 'mb-3'"
                      />
                      <b-form-text
                        v-if="query['vkMembersCount.from'] === ''"
                        class="mb-3"
                      >
                        Не важно
                      </b-form-text>
                    </b-col>

                    <b-col md="6">
                      <b-form-input
                        v-model="query['vkMembersCount.to']"
                        :disabled="query.hasVk !== 'YES'"
                        type="number"
                        placeholder="До"
                        min="0"
                        step="1"
                      />
                      <b-form-text v-if="query['vkMembersCount.to'] === ''">
                        Не важно
                      </b-form-text>
                    </b-col>
                  </b-row>
                </b-form-group>
              </b-col>
            </b-row>

            <b-row>
              <b-col md="6">
                <b-form-group label="Instagram">
                  <b-form-select
                    v-model="query.hasInstagram"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>

              <b-col md="6">
                <b-form-group label="Twitter">
                  <b-form-select
                    v-model="query.hasTwitter"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>
            </b-row>

            <b-row>
              <b-col md="6">
                <b-form-group label="YouTube">
                  <b-form-select
                    v-model="query.hasYoutube"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>

              <b-col md="6">
                <b-form-group label="Facebook">
                  <b-form-select
                    v-model="query.hasFacebook"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>
            </b-row>
          </b-form-group>
        </b-card>

        <b-card
          header="Сайт онлайн"
          class="mb-4"
        >
          <b-form-group>
            <b-row>
              <b-col md="4">
                <b-form-group>
                  <b-form-select
                    v-model="query.hasOnline"
                    :options="selectOptions"
                  />
                </b-form-group>
              </b-col>
            </b-row>
          </b-form-group>
        </b-card>
      </b-col>
    </b-row>

    <b-row>
      <b-col md="4" class="mb-4">
        <b-button
          v-if="loading.search"
          disabled
          pill
          block
          variant="primary"
        >
          <b-icon-arrow-clockwise animation="spin" />
          Найти
        </b-button>
        <b-button
          v-else
          pill
          block
          variant="primary"
        >
          <b-icon-search />
          Найти
        </b-button>
      </b-col>

      <b-col md="4" class="mb-4">
        <b-button
          v-if="loading.downloadEmails"
          disabled
          pill
          block
          variant="outline-primary"
        >
          <b-icon-arrow-clockwise animation="spin" />
          Скачать emails
        </b-button>
        <b-button
          v-else
          pill
          block
          variant="outline-primary"
        >
          <b-icon-envelope />
          Скачать emails
        </b-button>
      </b-col>

      <b-col md="4" class="mb-4">
        <b-button
          v-if="loading.downloadPhones"
          disabled
          pill
          block
          variant="outline-primary"
        >
          <b-icon-arrow-clockwise animation="spin" />
          Скачать телефоны
        </b-button>
        <b-button
          v-else
          pill
          block
          variant="outline-primary"
        >
          <b-icon-telephone />
          Скачать телефоны
        </b-button>
      </b-col>

      <b-alert
        fade
        :show="downloadAlertCountDown"
        dismissible
        variant="success"
        class="w-100"
        @dismissed="downloadAlertCountDown=0"
      >
        <h6 class="alert-heading">
          Скачивание началось
        </h6>

        <p>
          Наберитесь терпения, скачивание может идти ~1 минуту. Кстати, файл уже очищен от дубликатов
        </p>
      </b-alert>
    </b-row>

    <h3 class="pt-3 pb-3">
      Найдено
      <template v-if="company.items && company.items.length >= 20">
        более
      </template>
      <span class="text-muted">
        {{ (company.items && company.items.length) || 0 }}
      </span>
      компаний
    </h3>

    <CardDeck :items="company.items" />

    <Footer />
  </b-container>
</template>

<script lang="ts">
import Vue from 'vue'
import mock from "../customHelpers/mock";
import select from "../customHelpers/select";

export default Vue.extend({
  async asyncData (): Promise<object> {
    const res: any = await new Promise((resolve => setTimeout(() => {
      resolve(mock)
    },1)))

    return {
      company: {
        items: res.companies
      },
      titleCompaniesCount: 100,
      title: 'title'
    }
  },
  data (): any {
    return {
      selectOptions: [{
        text: 'Не важно',
        value: select.any
      }, {
        text: 'Да',
        value: select.yes
      }, {
        text: 'Нет',
        value: select.no
      }],
      query: {
        hasEmail: select.any,
        hasPhone: select.any,
        hasOnline: select.any,
        hasInn: select.any,
        hasKpp: select.any,
        hasOgrn: select.any,
        hasAppStore: select.any,
        hasGooglePlay: select.any,
        hasVk: select.any,
        'vkMembersCount.from': '',
        'vkMembersCount.to': '',
        hasInstagram: select.any,
        hasTwitter: select.any,
        hasYoutube: select.any,
        hasFacebook: select.any
      },
      city: {
        list: [],
        tags: [],
        search: '',
        addTag: '',
        removeTag: ''
      },
      category: {
        list: [],
        tags: [],
        search: '',
        addTag: '',
        removeTag: ''
      },
      loading: {
        search: false,
        downloadEmails: false,
        downloadPhones: false
      },
      downloadAlertCountDown: 0,
      downloadAlertDismissSecs: 10,
      scrollDone: false
    }
  },
  head () {
    return {
      title: this.title,
    }
  }
})
</script>
