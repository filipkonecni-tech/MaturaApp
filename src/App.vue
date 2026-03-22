<template>
  <div class="container">
    <h1>Matura App</h1>

    <div class="grid">

      <!-- Mustre -->
      <div class="card">
        <h2>Mustre</h2>

        <input v-model="nazivMustre" placeholder="Naziv mustre" />
        <select v-model="tipMatureMustre">
          <option disabled value="">Tip mature</option>
          <option>OPSTI</option>
          <option>STRUCNI</option>
          <option>UMETNICKI</option>
        </select>

        <input v-model="jezikMustre" placeholder="Jezik" />

        <select v-model="predmet1Mustre">
          <option disabled value="">Predmet 1</option>
          <option v-for="p in predmetiPrvi" :key="p" :value="p">{{ p }}</option>
        </select>

        <select v-model="predmet2Mustre">
          <option>Matematika</option>
        </select>

        <select v-model="predmet3Mustre">
  <option disabled value="">Predmet 3</option>
  <option v-for="p in predmet3MustreDostupni" :key="p" :value="p">{{ p }}</option>
</select>

        <button @click="addProfile">Sačuvaj mustru</button>

        <ul>
          <li v-for="p in mustre" :key="p.id">
            {{ p.naziv }} ({{ p.tip_mature }})
          </li>
        </ul>
      </div>

      <!-- Učenici -->
      <div class="card">
        <h2>Učenici</h2>

        <input v-model="imeStudent" placeholder="Ime i prezime" />
        <input v-model="odeljenjeStudent" placeholder="Odeljenje" />

        <label>Izaberi mustru:</label>
        <select v-model="selectedProfileId" @change="applyProfile">
          <option value="">Bez mustre</option>
          <option v-for="p in mustre" :key="p.id" :value="p.id">
            {{ p.naziv }}
          </option>
        </select>

        <div v-if="!selectedProfileId">
          <select v-model="tipMatureStudent">
            <option disabled value="">Tip mature</option>
            <option>OPSTI</option>
            <option>STRUCNI</option>
            <option>UMETNICKI</option>
          </select>

          <input v-model="jezikStudent" placeholder="Jezik" />

          <select v-model="predmet1Student">
            <option disabled value="">Predmet 1</option>
            <option v-for="p in predmetiPrvi" :key="p" :value="p">{{ p }}</option>
          </select>

          <select v-model="predmet2Student">
            <option>Matematika</option>
          </select>

          <select v-model="predmet3Student">
  <option disabled value="">Predmet 3</option>
  <option v-for="p in predmeti3StudentDostupni" :key="p" :value="p">{{ p }}</option>
</select>
        </div>

        <div v-else class="preview">
          <p><b>{{ tipMatureStudent }}</b></p>
          <p>{{ predmet1Student }} / {{ predmet2Student }} / {{ predmet3Student }}</p>
        </div>

        <button @click="addStudent">Dodaj učenika</button>

        <ul>
          <li v-for="s in studenti" :key="s.id">
            {{ s.ime }} - {{ s.tip_mature }} - {{ s.predmet1 }}, {{ s.predmet2 }}, {{ s.predmet3 }}
          </li>
        </ul>
      </div>

    </div>
  </div>
</template>

<script>
import { createClient } from "@supabase/supabase-js"

const supabase = createClient(import.meta.env.VITE_SUPABASE_URL,
  import.meta.env.VITE_SUPABASE_ANON_KEY
)
export default {
  data() {
    return {
      mustre: [],
      nazivMustre: "",
      tipMatureMustre: "",
      jezikMustre: "",
      predmet1Mustre: "",
      predmet2Mustre: "Matematika",
      predmet3Mustre: "",

      studenti: [],
      imeStudent: "",
      odeljenjeStudent: "",
      tipMatureStudent: "",
      jezikStudent: "",
      predmet1Student: "",
      predmet2Student: "Matematika",
      predmet3Student: "",
      selectedProfileId: null,

      jezici: ["Srpski",
"Albanski",
"Bosanski",
"Bugarski",
"Mađarski",
"Rumunski",
"Rusinski",
"Slovački",
"Hrvatskit"],
      predmetiPrvi: ["Srpski jezik i književnost",
"Albanski jezik i književnost",
"Bosanski jezik i književnost",
"Bugarski jezik i književnost",
"Mađarski jezik i književnost",
"Rumunski jezik i književnost",
"Rusinski jezik i književnost",
"Slovački jezik i književnost",
"Hrvatski jezik i književnost"],
      predmetiPoTipu: {
      OPSTI: [
        "Biologija","Geografija","Engleski jezik","Istorija","Italijski jezik",
        "Nemački jezik","Ruski jezik","Srpski kao nematernji jezik","Fizika",
        "Francuski jezik","Hemija","Španski jezik"
      ],
      STRUCNI: [
        "Zootehničar","Tehničar za biotehnologiju","Tehničar poljoprivredne tehnike",
        "Tehničar hortikulture","Brodomašinski tehničar","Mašinski tehničar za kompjutersko konstruisanje",
        "Mašinski tehničar merne i regulacione tehnike","Mašinski tehničar motornih vozila",
        "Tehničar grejanja i klimatizacije","Tehničar za kompjutersko upravljanje (CNC) mašina",
        "Tehničar za robotiku","Tehničar mašinske energetike","Tehničar optike",
        "Elektrotehničar automatike","Elektrotehničar elektromotornih pogona",
        "Elektrotehničar elektronike","Elektrotehničar energetike","Elektrotehničar za termičke i rashladne uređaje",
        "Elektrotehničar informacionih tehnologija","Elektrotehničar procesnog upravljanja","Elektrotehničar računara",
        "Tehničar grafičke dorade","Tehničar za zaštitu životne sredine","Tehničar za industrijsku farmaceutsku tehnologiju",
        "Tehničar štampe","Fotograf","Hemijski laborant","Hemijsko-tehnološki tehničar",
        "Tekstilni tehničar","Građevinski tehničar za laboratorijska ispitivanja",
        "Građevinski tehničar za hidrogradnju","Izvođač instalaterskih i završnih građevinskih radova",
        "Nautički tehničar – rečni smer","Saobraćajno-transportni tehničar","Tehničar vuče",
        "Tehničar PTT saobraćaja","Tehničar unutrašnjeg transporta","Transportni komercijalista",
        "Aranžer u trgovini i Trgovinski tehničar","Kulinarski tehničar","Ugostiteljski tehničar",
        "Ekonomski tehničar","Finansijski tehničar","Carinski tehničar",
        "Ginekološko-akušerska sestra","Zubni tehničar","Medicinska sestra – vaspitač",
        "Pedijatrijska sestra – tehničar","Sanitarno-ekološki tehničar","Scenski masker i vlasuljar"
      ],
      UMETNICKI: ["Solfeđo","Harmonija"]
    }
  }
},
computed: {
  predmeti3StudentDostupni() {
    return this.predmetiPoTipu[this.tipMatureStudent] || []
  },
  predmet3MustreDostupni() {
    return this.predmetiPoTipu[this.tipMatureMustre] || []
  }
},

  methods: {
    async loadProfiles() {
      const { data, error } = await supabase.from("mustre").select("*")
      if (error) return console.error(error)
      this.mustre = data
    },

    async loadStudents() {
      const { data, error } = await supabase.from("studenti").select("*")
      if (error) return console.error(error)
      this.studenti = data
    },

    applyProfile() {
      const profile = this.mustre.find(p => p.id === this.selectedProfileId)
      if (!profile) return

      this.tipMatureStudent = profile.tip_mature
      this.jezikStudent = profile.jezik
      this.predmet1Student = profile.predmet1
      this.predmet2Student = profile.predmet2
      this.predmet3Student = profile.predmet3
    },

    async addProfile() {
      const { data, error } = await supabase.from("mustre").insert({
    naziv: this.nazivMustre,
    tip_mature: this.tipMatureMustre,
    jezik: this.jezikMustre,
    predmet1: this.predmet1Mustre,
    predmet2: this.predmet2Mustre,
    predmet3: this.predmet3Mustre
  })
  console.log("Supabase response:", { data, error });

  if (error) alert("Greška: " + error.message);

  this.loadProfiles();
      this.nazivMustre = ""
      this.tipMatureMustre = ""
      this.jezikMustre = ""
      this.predmet1Mustre = ""
      this.predmet2Mustre = "Matematika"
      this.predmet3Mustre = ""
    },

    async addStudent() {
      const { data, error } = await supabase.from("studenti").insert({
        ime: this.imeStudent,
        odeljenje: this.odeljenjeStudent,
        tip_mature: this.tipMatureStudent,
        jezik: this.jezikStudent,
        predmet1: this.predmet1Student,
        predmet2: this.predmet2Student,
        predmet3: this.predmet3Student
      })

      if (error) return console.error(error)
      this.loadStudents()

      this.imeStudent = ""
      this.odeljenjeStudent = ""
      this.tipMatureStudent = ""
      this.jezikStudent = ""
      this.predmet1Student = ""
      this.predmet2Student = "Matematika"
      this.predmet3Student = ""
      this.selectedProfileId = null
    }
  },

  mounted() {
    this.loadProfiles()
    this.loadStudents()
  }
}
</script>

<style>
body {
  background: #f4f6f9;
}

.container {
  max-width: 1000px;
  margin: auto;
  font-family: Arial;
}

.grid {
  display: flex;
  gap: 20px;
}

.card {
  flex: 1;
  background: white;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 5px 15px rgba(0,0,0,0.1);
}

select {
  width: 100%;
  margin: 6px 0;
  padding: 8px;
  border-radius: 6px;
  border: 1px solid #ccc;
}
input {
  width: 95.5%;
  margin: 6px 0;
  padding: 8px;
  border-radius: 6px;
  border: 1px solid #ccc;
}

button {
  margin-top: 10px;
  padding: 10px;
  width: 100%;
  background: #4CAF50;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

button:hover {
  background: #45a049;
}

.preview {
  background: #eef;
  padding: 10px;
  border-radius: 8px;
  margin-top: 10px;
}
</style>