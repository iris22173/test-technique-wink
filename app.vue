<template>
  <div style="min-height:100vh; background:#f7f8fa; font-family:'Inter', sans-serif;">

    <!-- HEADER -->
    <header style="height:60px; background:white; border-bottom:1px solid #e5e7eb; display:flex; align-items:center; padding:0 40px;">
      <span style="font-size:22px; font-weight:700; color:#185BD3;">WINK</span>
    </header>

    <!-- STEPPER -->
    <div style="max-width:1100px;margin:30px auto;display:flex;justify-content:center;gap:80px;">
      <div style="text-align:center;">
        <div :style="stepStyle(1)">1</div>
        <p :style="stepLabelStyle(1)">Faisons connaissance</p>
      </div>
      <div style="text-align:center;">
        <div :style="stepStyle(2)">2</div>
        <p :style="stepLabelStyle(2)">Espace de travail</p>
      </div>
      <div style="text-align:center;">
        <div :style="stepStyle(3)">3</div>
        <p :style="stepLabelStyle(3)">À propos de vous</p>
      </div>
    </div>

    <!-- PAGE 1 ------------------------------------------------------------ -->
    <div v-if="currentStep === 1">
      <div style="max-width:1100px; margin:30px auto; background:white; padding:40px; border-radius:16px; border:1px solid #e5e7eb;">

        <h1 style="font-size:28px;font-weight:600;margin-bottom:30px;">Faisons connaissance</h1>

        <!-- Photo -->
        <label style="font-weight:600;">Photo de profil</label>

        <div style="display:flex;align-items:center;gap:20px;margin-top:10px;margin-bottom:30px;">
          <div style="width:90px;height:90px;border-radius:50%;background:#f3f4f6;border:1px solid #d1d5db;display:flex;align-items:center;justify-content:center;font-size:20px;">
            <img v-if="photoPreview" :src="photoPreview" style="width:100%;height:100%;border-radius:50%;object-fit:cover;" />
            <span v-else>AP</span>
          </div>

          <div>
            <input ref="photoRef" type="file" style="display:none;" accept="image/png,image/jpeg" @change="changePhoto" />
            <button @click="photoRef.click()" type="button"
              style="padding:8px 12px;border:1px solid #ccc;border-radius:6px;background:white;cursor:pointer;">
              Ajouter une photo
            </button>
            <button v-if="photoPreview" @click="clearPhoto" type="button"
              style="padding:8px 12px;border:1px solid #ff4d4d;background:#ffecec;color:#ff4d4d;border-radius:6px;margin-left:10px;cursor:pointer;">
              Supprimer
            </button>
            <p style="font-size:12px;color:#555;margin-top:6px;">Format PNG ou JPEG – max 5 Mo</p>
          </div>
        </div>

        <!-- Form -->
        <label>Prénom *</label>
        <input v-model="form.firstName" type="text"
          style="width:100%;padding:14px;border:1px solid #ccc;border-radius:8px;margin:6px 0 20px;" />

        <label>Nom *</label>
        <input v-model="form.lastName" type="text"
          style="width:100%;padding:14px;border:1px solid:#ccc;border-radius:8px;margin:6px 0 20px;" />

        <label>Email *</label>
        <input v-model="form.email" type="email"
          style="width:100%;padding:14px;border:1px solid:#ccc;border-radius:8px;margin:6px 0 30px;" />

        <button 
          @click="goToPage(2)" 
          :disabled="!validPage1"
          :style="buttonStyle(validPage1)"
        >
          Continuer →
        </button>

      </div>
    </div>

    <!-- PAGE 2 ------------------------------------------------------------ -->
    <div v-if="currentStep === 2">
      <div style="max-width:1100px;margin:30px auto;background:white;padding:40px;border-radius:16px;border:1px solid #e5e7eb;">

        <button @click="goToPage(1)"
          style="background:none;border:none;color:#555;font-size:14px;cursor:pointer;margin-bottom:20px;">
          ← Retour
        </button>

        <h1 style="font-size:28px;font-weight:600;margin-bottom:30px;">Créez votre espace de travail</h1>

        <!-- Logo -->
        <label style="font-weight:600;">Logo de l’entreprise</label>

        <div style="display:flex;align-items:center;gap:20px;margin-top:10px;margin-bottom:30px;">
          <div style="width:90px;height:90px;border-radius:12px;background:#f3f4f6;border:1px solid #d1d5db;display:flex;align-items:center;justify-content:center;overflow:hidden;">
            <img v-if="company.logoPreview" :src="company.logoPreview" style="width:100%;height:100%;object-fit:contain;padding:10px;" />
            <svg v-else width="40" height="40" fill="#888"><rect width="40" height="40" rx="8" /></svg>
          </div>

          <div>
            <input ref="companyLogoRef" type="file" accept="image/png,image/jpeg" style="display:none;" @change="uploadCompanyLogo" />
            <button @click="companyLogoRef.click()" type="button"
              style="padding:8px 14px;border:1px solid #ccc;border-radius:6px;background:white;font-size:14px;cursor:pointer;">
              Modifier la photo
            </button>
            <button v-if="company.logoPreview" @click="removeCompanyLogo" type="button"
              style="padding:8px 14px;border:1px solid #ff4d4d;background:#ffecec;color:#ff4d4d;border-radius:6px;margin-left:10px;cursor:pointer;">
              Supprimer
            </button>
            <p style="font-size:12px;color:#555;margin-top:6px;">au format *.png ou *.jpeg</p>
          </div>
        </div>

        <label>Nom de l’entreprise *</label>
        <input v-model="company.name"
          style="width:100%;padding:14px;border:1px solid #ccc;border-radius:8px;margin:6px 0 20px;" />

        <label>Description *</label>
        <textarea v-model="company.desc"
          style="width:100%;height:140px;padding:14px;border:1px solid #ccc;border-radius:8px;margin:6px 0 20px;">
        </textarea>

        <label>Site internet *</label>
        <div style="display:flex;gap:10px;margin:6px 0 20px;">
          <span style="background:#f3f4f6;border:1px solid #ccc;border-radius:8px;padding:14px;">https://</span>
          <input v-model="company.site"
            style="flex:1;padding:14px;border:1px solid #ccc;border-radius:8px;font-size:15px;" />
        </div>

        <div style="margin-top:30px;display:flex;justify-content:space-between;">
          <button @click="goToPage(1)"
            style="padding:12px 20px;border:1px solid #bbb;background:white;border-radius:8px;cursor:pointer;">
            Retour
          </button>

          <button 
            @click="goToPage(3)"
            :disabled="!validPage2"
            :style="buttonStyle(validPage2)"
          >
            Continuer →
          </button>
        </div>

      </div>
    </div>

    <!-- PAGE 3 ------------------------------------------------------------ -->
    <div v-if="currentStep === 3">
      <div style="max-width:1100px;margin:30px auto;background:white;padding:40px;border-radius:16px;border:1px solid #e5e7eb;">

        <button @click="goToPage(2)"
          style="background:none;border:none;color:#555;font-size:14px;cursor:pointer;margin-bottom:20px;">
          ← Retour
        </button>

        <h1 style="font-size:28px;font-weight:600;margin-bottom:30px;">Pour mieux vous connaître</h1>

        <label>Votre rôle *</label>
        <input v-model="about.role"
          style="width:100%;padding:14px;border:1px solid #ccc;border-radius:8px;margin:6px 0 20px;" />

        <label>Votre expérience *</label>
        <input v-model="about.experience"
          style="width:100%;padding:14px;border:1px solid #ccc;border-radius:8px;margin:6px 0 20px;" />

        <button 
          @click="finish()" 
          :disabled="!validPage3"
          :style="buttonStyle(validPage3)"
        >
          Terminer
        </button>

      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, reactive, computed } from "vue"

const currentStep = ref(1)

/* NAVIGATION */
function goToPage(n) {
  currentStep.value = n
}
function finish() {
  alert("🎉 Test complété avec succès !")
}

/* PAGE 1 */
const photoPreview = ref(null)
const photoRef = ref(null)

const form = reactive({
  firstName: "",
  lastName: "",
  email: ""
})

function changePhoto(e) {
  const file = e.target.files[0]
  if (!file) return
  const reader = new FileReader()
  reader.onload = () => (photoPreview.value = reader.result)
  reader.readAsDataURL(file)
}
function clearPhoto() {
  photoPreview.value = null
  if (photoRef.value) photoRef.value.value = ""
}

/* VALIDATION PAGE 1 */
const validPage1 = computed(() =>
  form.firstName.trim() !== "" &&
  form.lastName.trim() !== "" &&
  form.email.trim() !== ""
)

/* PAGE 2 */
const companyLogoRef = ref(null)
const company = reactive({
  logoPreview: null,
  name: "",
  desc: "",
  site: ""
})

function uploadCompanyLogo(e) {
  const file = e.target.files[0]
  if (!file) return
  const reader = new FileReader()
  reader.onload = () => (company.logoPreview = reader.result)
  reader.readAsDataURL(file)
}
function removeCompanyLogo() {
  company.logoPreview = null
}

/* VALIDATION PAGE 2 */
const validPage2 = computed(() =>
  company.name.trim() !== "" &&
  company.desc.trim() !== "" &&
  company.site.trim() !== ""
)

/* PAGE 3 */
const about = reactive({
  role: "",
  experience: ""
})

/* VALIDATION PAGE 3 */
const validPage3 = computed(() =>
  about.role.trim() !== "" &&
  about.experience.trim() !== ""
)

/* BUTTON STYLE */
function buttonStyle(isValid) {
  return `
    padding:14px 20px;
    border:none;
    border-radius:8px;
    font-weight:600;
    cursor:${isValid ? "pointer" : "not-allowed"};
    background:${isValid ? "#185BD3" : "#9BB8E0"};
    color:white;
    width:200px;
  `
}

/* STEPPER */
function stepStyle(n) {
  return `
    width:32px;height:32px;border-radius:50%;
    display:flex;align-items:center;justify-content:center;font-weight:600;
    ${currentStep.value === n ? "background:#185BD3;color:white;" : "background:#e5e7eb;color:#555;"}
  `
}
function stepLabelStyle(n) {
  return `
    margin-top:8px;
    ${currentStep.value === n ? "color:#185BD3;font-weight:600;" : "color:#777;"}
  `
}
</script>
