# Prompt Pembuat Diagram BPMN

Salin seluruh blok di bawah ini ke chat AI, lalu isi bagian dalam kurung kurawal ganda `{{...}}`. Bagian yang tidak kamu tahu jawabannya, tulis saja `tidak diketahui`. Jangan dikarang, karena aturan di dalam prompt sudah menyuruh AI-nya menandai lubang data.

---

```
Kamu berperan sebagai analis proses bisnis yang terbiasa memodelkan proses dengan
notasi BPMN 2.0 dan menuliskan hasilnya untuk naskah akademik.

## Konteks

- Organisasi: {{NAMA ORGANISASI, misal: sebuah gereja / koperasi / UMKM percetakan}}
- Proses yang dimodelkan: {{NAMA PROSES, misal: peminjaman aset}}
- Versi yang diminta: {{proses yang sedang berjalan ATAU proses usulan}}
- Pelaku yang terlibat: {{DAFTAR PERAN, misal: peminjam, koordinator, petugas gudang}}
- Pemicu proses (kapan proses dimulai): {{misal: ada tim yang butuh aset untuk acara}}
- Hasil akhir yang diharapkan: {{misal: aset kembali dan tercatat kondisinya}}
- Cara kerja saat ini: {{ceritakan apa adanya, termasuk yang manual, misal: permintaan
  lewat pesan WhatsApp, dicatat di buku, persetujuan lisan}}
- Masalah yang sudah diketahui: {{misal: tidak ada catatan siapa yang menyetujui}}
- Aturan atau kebijakan yang mengikat: {{misal: peminjaman di atas 3 hari perlu
  persetujuan koordinator; kosongkan kalau tidak ada}}

## Aturan pemodelan

1. Satu diagram untuk satu proses. Jangan menggabungkan dua proses berbeda ke dalam
   satu diagram.
2. Buat satu *pool* untuk organisasi, dan satu *lane* untuk tiap peran yang benar
   benar melakukan pekerjaan. Peran yang cuma menerima informasi tidak perlu lane
   sendiri.
3. Nama *task* memakai kata kerja aktif dan objeknya, misalnya "Memeriksa
   ketersediaan aset", bukan "Ketersediaan aset".
4. Tiap *gateway* eksklusif harus punya pertanyaan yang jelas dan semua cabangnya
   diberi label, termasuk cabang yang menolak atau membatalkan.
5. Proses harus punya satu *start event* dan boleh punya lebih dari satu *end event*,
   misalnya selesai, ditolak, dan dibatalkan. Jangan ada alur yang menggantung tanpa
   ujung.
6. Kalau ada penantian yang bergantung waktu, modelkan sebagai *timer event*, jangan
   sebagai task biasa.
7. Kalau ada komunikasi ke pihak di luar organisasi, gunakan *pool* terpisah yang
   dibiarkan kosong dan dihubungkan dengan *message flow*.
8. Jangan menambahkan langkah yang tidak disebutkan di bagian Konteks. Kalau sebuah
   langkah tampaknya wajib ada tapi tidak diceritakan, tulis langkah itu dan beri
   penanda [ASUMSI: ...] supaya bisa dikonfirmasi ke narasumber.
9. Jangan mengarang angka, durasi, nama orang, atau nama sistem. Tulis
   [BUTUH DATA: ...] di tempatnya.

## Format keluaran

Jawab dengan tiga bagian berurutan, tanpa basa basi pembuka.

### Bagian A. Daftar elemen

Sajikan sebagai tabel: Kode, Jenis elemen (start event, task, exclusive gateway,
timer event, end event), Nama, Lane, dan Menuju (kode elemen berikutnya, beserta
label cabang kalau berasal dari gateway).

Setelah tabel, tulis daftar titik keputusan beserta pertanyaan yang diajukan di
masing-masing gateway.

### Bagian B. Narasi untuk naskah

Tulis dalam bahasa Indonesia dengan tiga bagian:

1. Paragraf pengantar sebelum gambar: proses apa ini, siapa pelakunya, dan apa yang
   memicunya. Tutup dengan kalimat yang merujuk gambar, misalnya "Alurnya
   digambarkan pada Gambar X".
2. Baris penanda posisi gambar: [Gambar X: {{judul gambar}}]
3. Paragraf pembacaan setelah gambar: bagian mana dari alur yang penting, terutama
   titik yang menimbulkan masalah, dan apa akibatnya. Satu paragraf untuk satu
   temuan.

Aturan bahasa yang harus dipatuhi di Bagian B:

- Jangan memakai tanda pisah em-dash. Pecah kalimatnya atau pakai koma.
- Jangan memakai pembuka kosong seperti "Di era digital saat ini" atau "Tidak dapat
  dipungkiri bahwa". Mulai langsung dari fakta.
- Kata evaluatif seperti efisien, efektif, optimal, komprehensif, terintegrasi, atau
  signifikan hanya boleh dipakai kalau langsung dijelaskan isinya di kalimat yang
  sama, dengan angka, dengan perbandingan sebelum dan sesudah, atau dengan menyebut
  cakupannya. Kalau tidak bisa dijelaskan, buang kata itu dan tulis apa adanya.
- Istilah asing ditulis miring saat pertama muncul lalu dipakai konsisten.

### Bagian C. File BPMN 2.0 XML

Keluarkan satu blok kode XML utuh yang bisa langsung disimpan sebagai berkas .bpmn
dan diimpor ke Bizagi Modeler atau Camunda Modeler. Syaratnya:

- Memakai namespace BPMN 2.0 resmi, dengan elemen `<bpmn:collaboration>`,
  `<bpmn:participant>` untuk pool, dan `<bpmn:laneSet>` untuk lane.
- Setiap id unik dan konsisten antara bagian proses dan bagian diagram.
- Wajib menyertakan bagian `<bpmndi:BPMNDiagram>` lengkap dengan koordinat, karena
  tanpa itu diagramnya akan terbuka kosong. Setiap elemen punya `BPMNShape` dan
  setiap alur punya `BPMNEdge` dengan minimal dua `waypoint`.
- Konvensi tata letak supaya rapi: pool mulai di x=160 y=80 dengan lebar 1400. Tiap
  lane setinggi 250 dan ditumpuk ke bawah. Elemen mengalir ke kanan dengan jarak
  antar elemen 150. Ukuran baku: start dan end event 36x36, task 100x80, gateway
  50x50. Titik tengah vertikal elemen diletakkan di tengah lane-nya.
- Jangan menyingkat isi XML dengan komentar seperti "dan seterusnya". Tulis lengkap.

Struktur yang diharapkan kira kira seperti ini, ikuti polanya:

<?xml version="1.0" encoding="UTF-8"?>
<bpmn:definitions xmlns:bpmn="http://www.omg.org/spec/BPMN/20100524/MODEL"
                  xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI"
                  xmlns:dc="http://www.omg.org/spec/DD/20100524/DC"
                  xmlns:di="http://www.omg.org/spec/DD/20100524/DI"
                  id="Definitions_1" targetNamespace="http://bpmn.io/schema/bpmn">
  <bpmn:collaboration id="Collaboration_1">
    <bpmn:participant id="Participant_1" name="NAMA ORGANISASI" processRef="Process_1" />
  </bpmn:collaboration>
  <bpmn:process id="Process_1" isExecutable="false">
    <bpmn:laneSet id="LaneSet_1">
      <bpmn:lane id="Lane_1" name="NAMA PERAN">
        <bpmn:flowNodeRef>StartEvent_1</bpmn:flowNodeRef>
      </bpmn:lane>
    </bpmn:laneSet>
    <bpmn:startEvent id="StartEvent_1" name="NAMA PEMICU">
      <bpmn:outgoing>Flow_1</bpmn:outgoing>
    </bpmn:startEvent>
    <bpmn:sequenceFlow id="Flow_1" sourceRef="StartEvent_1" targetRef="Task_1" />
  </bpmn:process>
  <bpmndi:BPMNDiagram id="BPMNDiagram_1">
    <bpmndi:BPMNPlane id="BPMNPlane_1" bpmnElement="Collaboration_1">
      <bpmndi:BPMNShape id="Participant_1_di" bpmnElement="Participant_1" isHorizontal="true">
        <dc:Bounds x="160" y="80" width="1400" height="250" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape id="Lane_1_di" bpmnElement="Lane_1" isHorizontal="true">
        <dc:Bounds x="190" y="80" width="1370" height="250" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape id="StartEvent_1_di" bpmnElement="StartEvent_1">
        <dc:Bounds x="232" y="187" width="36" height="36" />
      </bpmndi:BPMNShape>
      <bpmndi:BPMNEdge id="Flow_1_di" bpmnElement="Flow_1">
        <di:waypoint x="268" y="205" />
        <di:waypoint x="330" y="205" />
      </bpmndi:BPMNEdge>
    </bpmndi:BPMNPlane>
  </bpmndi:BPMNDiagram>
</bpmn:definitions>

## Periksa sendiri sebelum menjawab

Sebelum mengirim jawaban, cek satu per satu dan perbaiki kalau ada yang meleset:

1. Semua elemen di Bagian A muncul di XML, dan sebaliknya.
2. Tidak ada elemen yang tidak punya alur masuk, kecuali start event.
3. Tidak ada elemen yang tidak punya alur keluar, kecuali end event.
4. Semua cabang gateway punya label.
5. Setiap id yang dirujuk di bagian diagram benar benar ada di bagian proses.
6. Setiap sequenceFlow punya BPMNEdge dengan waypoint.
7. Bagian B tidak memakai em-dash dan tidak memakai kata evaluatif yang menggantung.
8. Tidak ada angka, nama, atau durasi yang dikarang. Yang belum diketahui sudah
   ditandai [BUTUH DATA: ...] atau [ASUMSI: ...].

Kalau ada yang membuatmu ragu dan bisa membelokkan seluruh model, tanyakan dulu
maksimal tiga pertanyaan sebelum mengerjakan. Kalau tidak ada, langsung kerjakan.
```

---

## Cara pakai singkat

1. Isi bagian `{{...}}`. Bagian **Cara kerja saat ini** yang paling menentukan mutu hasilnya, jadi tulis sedetail yang kamu tahu dari lapangan.
2. Kirim. Kalau AI-nya balik nanya, jawab dulu, jangan dilewat.
3. Simpan Bagian C sebagai berkas berekstensi `.bpmn`, lalu impor ke Bizagi Modeler lewat menu File, Import, BPMN 2.0.
4. Di Bizagi, tata letaknya biasanya masih perlu dirapikan sedikit. Isi diagramnya yang penting sudah benar.
5. Untuk membuat pasangan diagram berjalan dan usulan, jalankan prompt dua kali dengan mengganti isi **Versi yang diminta**, dan pada versi usulan tambahkan kalimat: "Pertahankan urutan langkah dari versi berjalan kecuali pada titik yang memang diperbaiki, lalu sebutkan persis apa yang berubah."
