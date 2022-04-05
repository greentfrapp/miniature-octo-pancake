<template>
  <h1>Top Secret Woooo</h1>
  <p>Speak to you-know-who</p>
  <div class="text-left w-96 mx-auto flex flex-col gap-2">
    <h2>Sample questions <br/>(click one or type anything you want):</h2>
    <p class="bg-gray-100 cursor-pointer p-1" v-for="question in sampleQuestions" :key="question" @click="submitQuestion(question)">{{ question }}</p>
  </div>
  <div class="flex flex-col w-96 mx-auto gap-4 mt-4">
    <input type="text" @keyup.enter="submit" v-model="query" placeholder="Type your question here..." />
    <button @click="submit" class="p-2 bg-gray-200">{{ loading ? 'Loading...' : 'Submit' }}</button>
    <textarea v-model="response" readonly rows="10" />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import { Configuration, OpenAIApi } from 'openai'

export default defineComponent({
  mounted () {
    const configuration = new Configuration({
      organization: 'org-pgTqG9vntzJg1aU5uaPZyHZQ',
      apiKey: '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',
    })
    this.openai = new OpenAIApi(configuration)
  },
  data () {
    return {
      openai: null as any,
      query: '' as any,
      response: '',
      loading: false,
      sampleQuestions: [
        'Who are you?',
        'Who are your children?',
        'What do you think about China?',
        'Why did you name our nuke Bobo?',
      ],
      lambdaUrl: 'https://7nnt93gjve.execute-api.us-east-1.amazonaws.com/default/lky-demo',
    }
  },
  methods: {
    async submit () {
      this.sendQuery(`This is how Lee Kuan Yew, minister mentor of Singapore, would have responded to the following questions:\n\nQ: What were you doing during the Japanese occupation from 1942 to 1945?\nA:Well, I was a student at Raffles College, now the University of Singapore, doing English literature. Mathematics and Economics. And the Japanese came, knocked us about, and the three-and-a-half years was really a nightmare. I'm not quite sure whether it was worse to have been in a Japanese prisoner of war camp, or worse to have been a member of their Co-Prosperity Sphere outside their prisoner of war camps, working for their prosperity.\n\nQ:${this.query}\nA:`)
    },
    async submitQuestion (question:string) {
      this.query = question
      this.sendQuery(`This is how Lee Kuan Yew, minister mentor of Singapore, would have responded to the following questions:\n\nQ: What were you doing during the Japanese occupation from 1942 to 1945?\nA:Well, I was a student at Raffles College, now the University of Singapore, doing English literature. Mathematics and Economics. And the Japanese came, knocked us about, and the three-and-a-half years was really a nightmare. I'm not quite sure whether it was worse to have been in a Japanese prisoner of war camp, or worse to have been a member of their Co-Prosperity Sphere outside their prisoner of war camps, working for their prosperity.\n\nQ:${question}\nA:`)
    },
    sendQuery (query:string) {
      this.loading = true
      fetch(this.lambdaUrl, {
        method: 'POST',
        body: JSON.stringify({
          query,
        })
      }).then(async response => {
        this.response = await response.json()
        this.loading = false
      })
    },
  }
})
</script>

