<template>
  <v-col class="m4" cols="10" lg="6">
    <v-row class="text-h4 mb-4">
      <h4>
        <v-icon class="mr-2" color="primary">mdi-link-variant</v-icon
        ><a href="https://webvpn.hfut.edu.cn/" target="_blank">WEBVPN</a> URL
        Converter
      </h4>
    </v-row>

    <v-row>
      <p class="text--secondary mb-8">
        🥑 <b>WEBVPN Converter</b> 能帮助你将任意HFUT内网地址转换为WEBVPN地址。
      </p>
    </v-row>

    <v-row align="center" justify="center">
      <v-text-field
        label="Original URL"
        v-model="originalUrl"
        placeholder="http://one.hfut.edu.cn"
      />
      <v-tooltip bottom>
        <template v-slot:activator="{ on, attrs }">
          <v-btn icon v-bind="attrs" v-on="on" @click="clearInputUrl">
            <v-icon>mdi-delete</v-icon>
          </v-btn>
        </template>
        <span>Clear input</span>
      </v-tooltip>
    </v-row>

    <v-row align="center" justify="center" class="my-4">
      <v-col cols="12" sm="4" class="text-center">
        <v-btn class="mx-auto" color="primary" @click="convert()">
          <v-icon left>mdi-lan</v-icon>WEBVPN
        </v-btn>
      </v-col>
    </v-row>

    <v-row align="center" justify="center">
      <v-text-field
        label="WEBVPN"
        v-model="webvpnUrl"
        readonly
        placeholder="https://webvpn.hfut.edu.cn/..."
      />

      <v-tooltip bottom>
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            text
            color="primary"
            v-bind="attrs"
            v-on="on"
            target="_blank"
            :href="webvpnUrl"
          >
            OPEN
          </v-btn>
        </template>
        <span>Open URL in new tab</span>
      </v-tooltip>

      <v-tooltip bottom>
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            icon
            color="primary"
            v-bind="attrs"
            v-on="on"
            @click="copy(webvpnUrl)"
          >
            <v-icon>mdi-content-copy</v-icon>
          </v-btn>
        </template>
        <span>Copy WEBVPN URL</span>
      </v-tooltip>
    </v-row>

    <v-row class="mt-8">
      <p class="text--secondary text-uppercase">
        Common resources
      </p>
    </v-row>

    <v-row>
      <template v-for="resource in commonResources">
        <v-hover v-slot:default="{ hover }" :key="resource.url">
          <v-tooltip bottom>
            <template v-slot:activator="{ on, attrs }">
              <v-chip
                :class="hover ? 'mr-2 on-hover' : 'mr-2'"
                :href="encryptUrl(resource.url)"
                target="_blank"
                v-bind="attrs"
                v-on="on"
                outlined
                label
              >
                {{ resource.name }}
                <v-icon small right color="primary">mdi-open-in-new</v-icon>
              </v-chip>
            </template>
            <span>{{ resource.url }}</span>
          </v-tooltip>
        </v-hover>
      </template>
    </v-row>

    <v-snackbar
      v-model="snackbarCopy"
      max-width="100"
      rounded
      top
      timeout="2000"
    >
      <v-icon color="success">mdi-check</v-icon> {{ snackbarCopyNote }}
    </v-snackbar>
    <v-snackbar
      v-model="snackbarNoInput"
      max-width="100"
      rounded
      top
      timeout="2000"
    >
      <v-icon color="warning">mdi-alert</v-icon> You'll need to specify a URL
      first.
    </v-snackbar>
  </v-col>
</template>

<script lang="ts">
import Vue from 'vue'
import { convert } from './convert'

export default Vue.extend({
  name: 'Converter',

  data: () => ({
    snackbarCopy: false,
    snackbarCopyNote: 'Copied to clipboard!',
    snackbarNoInput: false,

    originalUrl: '',
    webvpnUrl: '',

    commonResources: [
      {
        name: 'GitLab',
        url: '172.17.33.100'
      },
      {
        name: '信息门户',
        url: 'one.hfut.edu.cn'
      },
      {
        name: '教学管理',
        url: 'jxglstu.hfut.edu.cn/eams5-student'
      },
      {
        name: '中国知网',
        url: 'www.cnki.net'
      },
      {
        name: '图书馆',
        url: 'lib.hfut.edu.cn'
      },
      {
        name: '外语平台',
        url: '172.31.241.173'
      }
    ]
  }),

  methods: {
    convert (): void {
      const originalUrl = this.originalUrl
      if (originalUrl === '') {
        this.snackbarNoInput = !this.snackbarNoInput
      } else {
        this.webvpnUrl = this.encryptUrl(originalUrl)
      }
    },

    encryptUrl (url: string): string {
      return `https://webvpn.hfut.edu.cn${convert(url)}`
    },

    clearInputUrl (): void {
      this.originalUrl = ''
    },

    copy (value: string): void {
      if (this.webvpnUrl === '') {
        value = 'https://webvpn.hfut.edu.cn'
        this.snackbarCopyNote = 'Default WEBVPN URL copied!'
      }
      console.log('Copied value:', value)
      // eslint-disable-next-line
      ;(this as any).$clipboard(value)
      this.snackbarCopy = !this.snackbarCopy
    }
  }
})
</script>

<style scoped>
.v-chip {
  transition: all 0.2s ease-in-out;
}

.v-chip.on-hover {
  border: thin solid var(--v-primary-base);
}
</style>
