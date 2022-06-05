<template>
<div>
<p>{{emailSelection.emails.size}} emails selected</p>
 <table class="mail-table">
    <tbody>
      <tr v-for="email in unarchivedEmails"
          :key="email.id"
          :class="['clickable', email.read ? 'read' : '']"
          >
        <td>
          <input type="checkbox"
@click="emailSelection.toggle(email)"
                 :selected="emailSelection.emails.has(email)" />
        </td>
<td>
<p>{{emailSelection.emails.size}} emails selected</p>
</td>
        <td @click="openEmail(email)">
{{email.from}}</td>
        <td @click="openEmail(email)">

          <p><strong>{{email.subject}}</strong> - {{email.body}}</p>
        </td>
        <td class="date"@click="openEmail(email)">
{{format(new Date(email.sentAt), 'MMM do yyyy')}}</td>
        <td><button @click="email.archived = true">Archive</button></td>
      </tr>
    </tbody>
  </table>
<MailView v-if="openedEmail" :email="openedEmail" />
</div>
</template>

<script>
import { format } from 'date-fns';
 import { ref, reactive } from 'vue';
 let selected = new Set()
      let emailSelection = {
        emails: selected,
        toggle(email) {
          if(selected.has(email)) {
            selected.delete(email)
          } else {
            selected.add(email)
          }
          console.log(selected)
        }
}
export default {
  data(){
    return {
 emailSelection,
      format,
      openedEmail: null,
      "emails": [
      ]
    }
  },
asyncData(context){
return context.$axios.get('http://localhost:3000/emails').then(response => {
return {
emails: response.data
}
})
},

  computed: {
    sortedEmails() {
      return this.emails.sort((e1, e2) => {
        return e1.sentAt < e2.sentAt ? 1 : -1
      })
    },
    unarchivedEmails() {
      return this.sortedEmails.filter(e => !e.archived)
    }
  },
  methods: {
      openEmail(email) {
        email.read = true
this.openedEmail = email
      }
}
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
/* Global Styles */
button {
  font-size: 16px;
  padding: 8px;
  border-radius: 3px;
  margin: 5px 10px 5px 0px;
  cursor: pointer;
}
button:disabled {
  cursor: auto;
}
button.selected {
  cursor: auto;
  color: black;
  border-color: black;
  border-width: 2px;
}
.clickable {
  cursor: pointer;
}
input[type='checkbox'] {
  -webkit-appearance:none;
  cursor: pointer;
  width:24px;
  height:24px;
  background:white;
  border-radius: 2px;
  border: 1px solid #555;
  position: relative;
  vertical-align: middle;
  padding: 10px;
}
input[type='checkbox'].partial-check {
  background: #ABC;
}
input[type='checkbox']:checked {
  background: #679;
}
.mb-0 {
  margin-bottom: 0;
}
/* Modal */
.modal, .overlay {
  width: 100%;
  height: 100%;
  position: fixed;
  left: 0;
  top: 0;
}
.overlay {
  opacity: 0.5;
  background-color: black;
}
.modal-card {
  position: relative;
  max-width: 80%;
  margin: auto;
  margin-top: 30px;
  padding: 20px;
  background-color: white;
  min-height: 500px;
  z-index: 10;
  opacity: 1;
}
/* Email Modal */
.email-display {
  text-align: left;
}
/* Mail Table */
.mail-table {
  max-width: 1000px;
  margin: auto;
  border-collapse: collapse;
}
.mail-table tr.read {
  background-color: #EEE;
}
.mail-table tr {
  height: 40px;
}
.mail-table td {
  border-bottom: 1px solid black;
  padding: 5px;
  text-align: left;
}
.mail-table tr:first-of-type td {
  border-top: 1px solid black;
}
.mail-table td p {
  max-height: 1.2em;
  overflow-y: hidden;
  margin: 0;
}
.mail-table td.date {
  width: 120px;
}
/* Bulk Action Bar */
.bulk-action-bar {
  width: 100%;
  max-width: 1000px;
  margin: auto;
  text-align: left;
  padding-bottom: 8px;
}
.bulk-action-bar input {
  margin: 5px;
}
.bulk-action-bar .checkbox {
  margin-right: 6px;
  margin-left: 3px;
}
</style>
