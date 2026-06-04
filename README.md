# Web Tasarım Dersi - API Ödevi

Hocam merhaba, tarayıcımdaki teknik bir çökme hatasından (RESULT_CODE_KILLED_BAD_MESSAGE) dolayı dosyaları GitHub'a ayrı ayrı yükleyemedim. Projenin çalışan hali **web-odevi.rar** dosyasının içerisindedir. 

Kodları bilgisayarınıza indirmeden tarayıcı üzerinden inceleyebilmeniz için aşağıya da aynen ekliyorum:

## ⚡ JavaScript Kodlarım (script.js)
```javascript
var apiURL = "https://jsonplaceholder.typicode.com/todos";
var tumGorevler = []; 
var duzenlemeModu = false;

var listelemekAlani = document.getElementById("gorevListesi");
var gorevKutusu = document.getElementById("gorevInput");
var buton = document.getElementById("kaydetButon");
var formBasligi = document.getElementById("formBaslik");
var gizliIdKutusu = document.getElementById("guncellenecekId");
var aramaKutusu = document.getElementById("aramaInput");

document.addEventListener("DOMContentLoaded", verileriGetir);
buton.addEventListener("click", formuKaydet);
aramaKutusu.addEventListener("input", listeyiEkranaBas);

function verileriGetir() {
    fetch(apiURL + "?_limit=6")
        .then(function(cevap) {
            return cevap.json();
        })
        .then(function(gelenVeriler) {
            tumGorevler = gelenVeriler;
            listeyiEkranaBas();
        });
}

function listeyiEkranaBas() {
    listelemekAlani.innerHTML = "";
    var arananMetin = aramaKutusu.value.toLowerCase();

    for (var i = 0; i < tumGorevler.length; i++) {
        var gorev = tumGorevler[i];
        
        if (gorev.title.toLowerCase().indexOf(arananMetin) === -1) {
            continue; 
        }

        var li = document.createElement("li");
        li.innerHTML = "<span>" + gorev.title + "</span>";

        var butonGrup = document.createElement("div");
        
        var duzenleBtn = document.createElement("button");
        duzenleBtn.innerText = "Düzenle";
        duzenleBtn.className = "duzenle-btn";
        duzenleBtn.setAttribute("onclick", "duzenleHazirlik(" + gorev.id + ")");
        
        var silBtn = document.createElement("button");
        silBtn.innerText = "Sil";
        silBtn.className = "sil-btn";
        silBtn.setAttribute("onclick", "gorevSil(" + gorev.id + ")");

        butonGrup.appendChild(duzenleBtn);
        butonGrup.appendChild(silBtn);
        li.appendChild(butonGrup);
        listelemekAlani.appendChild(li);
    }
}

function formuKaydet() {
    var metin = gorevKutusu.value;
    if (metin === "") {
        alert("Lütfen bir görev yazın!");
        return;
    }

    if (duzenlemeModu === true) {
        var id = gizliIdKutusu.value;
        gorevGuncelle(id, metin);
    } else {
        fetch(apiURL, {
            method: "POST",
            body: JSON.stringify({
                title: metin,
                completed: false
            }),
            headers: {
                "Content-type": "application/json; charset=UTF-8"
            }
        })
        .then(function(cevap) {
            return cevap.json();
        })
        .then(function(yeniGorev) {
            yeniGorev.id = Math.floor(Math.random() * 10000); 
            tumGorevler.push(yeniGorev);
            listeyiEkranaBas();
            formuTemizle();
            alert("Görev başarıyla eklendi!");
        });
    }
}

function gorevSil(id) {
    var eminMi = confirm("Bu görevi silmek istiyor musunuz?");
    if (eminMi === false) return;

    fetch(apiURL + "/" + id, {
        method: "DELETE"
    })
    .then(function() {
        var yeniDizi = [];
        for (var i = 0; i < tumGorevler.length; i++) {
            if (tumGorevler[i].id !== id) {
                yeniDizi.push(tumGorevler[i]);
            }
        }
        tumGorevler = yeniDizi;
        listeyiEkranaBas();
        alert("Görev silindi!");
    });
}

function duzenleHazirlik(id) {
    var bulunanGorev = null;
    for (var i = 0; i < tumGorevler.length; i++) {
        if (tumGorevler[i].id === id) {
            bulunanGorev = tumGorevler[i];
            break;
        }
    }

    if (bulunanGorev !== null) {
        gorevKutusu.value = bulunanGorev.title;
        gizliIdKutusu.value = bulunanGorev.id;
        duzenlemeModu = true;
        formBasligi.innerText = "Görevi Düzenle";
        buton.innerText = "Güncelle";
    }
}

function gorevGuncelle(id, yeniMetin) {
    fetch(apiURL + "/" + id, {
        method: "PUT",
        body: JSON.stringify({
            title: yeniMetin
        }),
        headers: {
            "Content-type": "application/json; charset=UTF-8"
        }
    })
    .then(function() {
        for (var i = 0; i < tumGorevler.length; i++) {
            if (tumGorevler[i].id == id) {
                tumGorevler[i].title = yeniMetin;
                break;
            }
        }
        listeyiEkranaBas();
        formuTemizle();
        alert("Görev güncellendi!");
    });
}

function formuTemizle() {
    gorevKutusu.value = "";
    gizliIdKutusu.value = "";
    duzenlemeModu = false;
    formBasligi.innerText = "Yeni Görev Ekle";
    buton.innerText = "Ekle";
}
