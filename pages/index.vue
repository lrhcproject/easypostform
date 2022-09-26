<template>
  <v-form>
    <v-container>
      <v-row>
        <v-col>
          <v-radio-group v-model="caseType" row>
            <v-radio label="やったほうがいいこと" value="shouldDo" />
            <v-radio label="やってはいけないこと" value="shouldNotDo" />
          </v-radio-group>
        </v-col>
      </v-row>
      <h1>5W1H+Then状況説明</h1>
      <v-row>
        <v-col>
          <v-text-field
            v-model="who"
            label="Who(だれが)"
            :placeholder="whoPlaceholder"
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field
            v-model="when"
            label="When(いつ)"
            :placeholder="whenPlaceholder"
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field
            v-model="where"
            label="Where(どこで)"
            :placeholder="wherePlaceholder"
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field
            v-model="why"
            label="Why(なんのために)"
            :placeholder="whyPlaceholder"
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field
            v-model="what"
            label="What(なにを)"
            :placeholder="whatPlaceholder"
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field
            v-model="how"
            label="How(どのようにした)"
            :placeholder="howPlaceholder"
          />
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field
            v-model="then"
            label="Then(どうなった)"
            :placeholder="thenPlaceholder"
          />
        </v-col>
      </v-row>
      <h1>
        {{
          caseType === 'shouldDo'
            ? 'どうしてやったほうがいいのか（理由の抽象化）'
            : 'どうしてやってはいけないのか（理由の抽象化）'
        }}
      </h1>
      <v-row
        v-for="(reason, index) in reasons"
        :key="`reasons[${index}]`"
        justify="space-between"
        align="center"
      >
        <v-col cols="12" sm="11">
          <v-textarea
            v-model="reasons[index]"
            :placeholder="reasonPlaceholders[index] || ''"
          />
        </v-col>
        <v-col cols="12" sm="1">
          <v-btn small color="warning" fab @click="spliceReasons(index)">
            <v-icon>mdi-minus</v-icon>
          </v-btn>
        </v-col>
      </v-row>
      <v-row justify="end">
        <v-col cols="12" sm="1">
          <v-btn small color="accent" fab @click="pushReasons()">
            <v-icon>mdi-plus</v-icon>
          </v-btn>
        </v-col>
      </v-row>
      <h1>
        {{
          caseType === 'shouldDo'
            ? 'やらなかったらどうなったか'
            : 'どのようにすればよかったか（代替行動案）'
        }}
      </h1>
      <v-row
        v-for="(alternative, index) in alternatives"
        :key="`alternatives[${index}]`"
        justify="space-between"
        align="center"
      >
        <v-col cols="12" sm="11">
          <v-textarea
            v-model="alternatives[index]"
            :placeholder="alternativePlaceholders[index] || ''"
          />
        </v-col>
        <v-col cols="12" sm="1">
          <v-btn small color="warning" fab @click="spliceAlternatives(index)">
            <v-icon>mdi-minus</v-icon>
          </v-btn>
        </v-col>
      </v-row>
      <v-row justify="end">
        <v-col cols="12" sm="1">
          <v-btn small color="accent" fab @click="pushAlternatives()">
            <v-icon>mdi-plus</v-icon>
          </v-btn>
        </v-col>
      </v-row>
      <h1>備考</h1>
      <v-row
        v-for="(footnote, index) in footnotes"
        :key="`footnotes[${index}]`"
        justify="space-between"
        align="center"
      >
        <v-col cols="12" sm="11">
          <v-textarea
            v-model="footnotes[index]"
            :placeholder="footnotePlaceholders[index] || ''"
          />
        </v-col>
        <v-col cols="12" sm="1">
          <v-btn small color="warning" fab @click="spliceFootnotes(index)">
            <v-icon>mdi-minus</v-icon>
          </v-btn>
        </v-col>
      </v-row>
      <v-row justify="end">
        <v-col cols="12" sm="1">
          <v-btn small color="accent" fab @click="pushFootnotes()">
            <v-icon>mdi-plus</v-icon>
          </v-btn>
        </v-col>
      </v-row>
      <v-btn elevation="2" @click="goToSubmit()">
        内容をクリップボードにコピーし投稿ページへ
      </v-btn>
    </v-container>
  </v-form>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      caseType: 'shouldDo',
      who: '',
      when: '',
      where: '',
      why: '',
      what: '',
      how: '',
      then: '',
      reasons: [],
      alternatives: [],
      footnotes: [],
      shouldDoPlaceholder: {
        who: '筆者が',
        when: '昨日',
        where: '自宅で',
        why: '空腹感を解消するために',
        what: '食事を',
        how: '摂った。',
        then: '空腹による苦痛から解放された。',
        reasons: ['ヒトは健康的に生活するために食事を必要としている。'],
        alternatives: [
          '苦痛が長引く。',
          '学業や仕事のパフォーマンスを損う。',
          '健康を損ない、最悪の場合は死に至る',
        ],
        footnotes: [],
      },
      shouldNotDoPlaceholder: {
        who: '筆者が',
        when: '昨日',
        where: '自宅で',
        why: 'ストレスを解消するために',
        what: 'ビールを',
        how: '大量に飲んだ。',
        then: 'アルコール中毒になり、長時間苦しんだ。',
        reasons: ['アルコールを過剰摂取することは健康上のリスクである。'],
        alternatives: [
          'お酒は適度に楽しむ。',
          'アルコール摂取以外のストレス解消法も試す。',
        ],
        footnotes: [],
      },
    }
  },
  computed: {
    whoPlaceholder() {
      return this.caseType === 'shouldDo'
        ? this.shouldDoPlaceholder.who
        : this.shouldNotDoPlaceholder.who
    },
    whenPlaceholder() {
      return this.caseType === 'shouldDo'
        ? this.shouldDoPlaceholder.when
        : this.shouldNotDoPlaceholder.when
    },
    wherePlaceholder() {
      return this.caseType === 'shouldDo'
        ? this.shouldDoPlaceholder.where
        : this.shouldNotDoPlaceholder.where
    },
    whyPlaceholder() {
      return this.caseType === 'shouldDo'
        ? this.shouldDoPlaceholder.why
        : this.shouldNotDoPlaceholder.why
    },
    whatPlaceholder() {
      return this.caseType === 'shouldDo'
        ? this.shouldDoPlaceholder.what
        : this.shouldNotDoPlaceholder.what
    },
    howPlaceholder() {
      return this.caseType === 'shouldDo'
        ? this.shouldDoPlaceholder.how
        : this.shouldNotDoPlaceholder.how
    },
    thenPlaceholder() {
      return this.caseType === 'shouldDo'
        ? this.shouldDoPlaceholder.then
        : this.shouldNotDoPlaceholder.then
    },
    reasonPlaceholders() {
      return this.caseType === 'shouldDo'
        ? this.shouldDoPlaceholder.reasons
        : this.shouldNotDoPlaceholder.reasons
    },
    alternativePlaceholders() {
      return this.caseType === 'shouldDo'
        ? this.shouldDoPlaceholder.alternatives
        : this.shouldNotDoPlaceholder.alternatives
    },
    footnotePlaceholders() {
      return this.caseType === 'shouldDo'
        ? this.shouldDoPlaceholder.footnotes
        : this.shouldNotDoPlaceholder.footnotes
    },
  },
  methods: {
    spliceReasons(index) {
      this.reasons.splice(index, 1)
    },
    pushReasons() {
      this.reasons.push('')
    },
    spliceAlternatives(index) {
      this.alternatives.splice(index, 1)
    },
    pushAlternatives() {
      this.alternatives.push('')
    },
    spliceFootnotes(index) {
      this.footnotes.splice(index, 1)
    },
    pushFootnotes() {
      this.footnotes.push('')
    },
    async goToSubmit() {
      let content = ''
      content += '*5W1H+Then状況説明\n'
      content += `|Who(だれが)|${this.who}|\n`
      content += `|When(いつ)|${this.when}|\n`
      content += `|Where(どこで)|${this.where}|\n`
      content += `|Why(なんのために)|${this.why}|\n`
      content += `|What(なにを)|${this.what}|\n`
      content += `|How(どのようにした)|${this.how}|\n`
      content += `|Then(どうなった)|${this.then}|\n`
      content +=
        this.caseType === 'shouldDo'
          ? '*どうしてやったほうがいいのか（理由の抽象化）\n'
          : '*どうしてやってはいけないのか（理由の抽象化）\n'
      for (const reason of this.reasons) content += `-${reason}\n`
      content +=
        this.caseType === 'shouldDo'
          ? '*やらなかったらどうなったか\n'
          : '*どのようにすればよかったか（代替行動案）\n'
      for (const alternative of this.alternatives)
        content += `-${alternative}\n`
      content += '*備考\n'
      for (const footnote of this.footnotes) content += `-${footnote}\n`
      await window.navigator.clipboard.writeText(content)
      window.location.assign(
        'https://seesaawiki.jp/lets-reveal-hidden-curriculum/e/add'
      )
    },
  },
}
</script>
