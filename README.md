# 🦠 HB Sickness System

QBCore tabanlı FiveM sunucuları için gelişmiş hastalık sistemi. Oyuncular can düşük olduğunda enfeksiyon kapabilir ve hastalık evreleri boyunca çeşitli efektler yaşayabilirler.

## 📋 İçindekiler

- [Özellikler](#özellikler)
- [Kurulum](#kurulum)
- [Konfigürasyon](#konfigürasyon)
- [Hastalık Sistemi](#hastalık-sistemi)
- [Antibiyotik Sistemi](#antibiyotik-sistemi)
- [Admin Komutları](#admin-komutları)
- [API](#api)
- [Sorun Giderme](#sorun-giderme)

## ✨ Özellikler

### 🎮 **Temel Özellikler**
- ✅ **Gerçekçi hastalık sistemi** - Can düşük olduğunda enfeksiyon riski
- ✅ **İki evreli hastalık** - Zamanla ilerleyen semptomlar
- ✅ **Antibiyotik sistemi** - Cooldown ve evre bazlı iyileşme
- ✅ **Görsel efektler** - Bulanıklık, sarhoş yürüyüş, göz efekti
- ✅ **Öksürme animasyonu** - Evre bazlı sıklık
- ✅ **Bulaşma sistemi** - Yakındaki oyunculara bulaşma riski

### 🎨 **Görsel Efektler**
- **Bulanıklık efekti** - Evre bazlı yoğunluk
- **Sarhoş yürüyüş** - Sadece 2. evrede aktif
- **Göz efekti** - 2. evrede gözler kapanıp açılıyor
- **Öksürme animasyonu** - Gerçekçi ses ve hareket

### ⏰ **Zaman Sistemi**
- **Enfeksiyon riski** - 3 dakika bekleme süresi
- **Evre geçişi** - 15 dakika sonra 2. evre
- **Antibiyotik cooldown** - 1 saat bekleme
- **Hatırlatma sistemi** - 30 dakikada bir uyarı

## 🚀 Kurulum

### 1. **Server.cfg'ye Ekleyin**
```cfg
ensure hb_sicksystem
```

**⚠️ Önemli:** Script ismi `hb_sicksystem` olarak kalmalıdır. İsim değiştirilirse script çalışmayacaktır.

### 3. **QBCore Bağımlılığı**
Script QBCore framework'ü gerektirir. `qb-core` resource'unuzun çalıştığından emin olun.

### 4. **Antibiyotik Eşyasını Ekleyin**
`qb-core/shared/items.lua` dosyasına ekleyin:
```lua
['antibiotic'] = {
    ['name'] = 'antibiotic',
    ['label'] = 'Antibiotic',
    ['weight'] = 100,
    ['type'] = 'item',
    ['image'] = 'antibiotic.png',
    ['unique'] = false,
    ['useable'] = true,
    ['shouldClose'] = true,
    ['combinable'] = nil,
    ['description'] = 'Hastalığı tedavi eder'
},
```

## ⚙️ Konfigürasyon

### **Temel Ayarlar**
```lua
Config.Sickness = {
    healthThreshold = 80,        -- Can yüzdesi (altında hastalık riski)
    infectionTime = 3 * 60 * 1000, -- Enfeksiyon bekleme süresi (3 dakika)
    infectionRange = 2.0,        -- Bulaşma mesafesi (metre)
}
```

### **Hastalık Evreleri**
```lua
Config.Sickness.stages = {
    stage1 = {
        duration = 15 * 15 * 1000,    -- 15 dakika
        antibioticsNeeded = 2,        -- Gerekli antibiyotik sayısı
        coughInterval = 30 * 1000,    -- Öksürme aralığı (30 saniye)
        blurIntensity = 0.2,          -- Bulanıklık yoğunluğu
        drunkWalkIntensity = 0.0,     -- Yürüyüş efekti (0 = yok)
        eyeEffect = false             -- Göz efekti
    },
    stage2 = {
        duration = 16 * 60 * 1000,    -- 16 dakika
        antibioticsNeeded = 6,        -- Gerekli antibiyotik sayısı
        coughInterval = 15 * 1000,    -- Öksürme aralığı (15 saniye)
        blurIntensity = 0.8,          -- Bulanıklık yoğunluğu
        drunkWalkIntensity = 0.3,     -- Yürüyüş efekti
        eyeEffect = true              -- Göz efekti aktif
    }
}
```

### **Antibiyotik Ayarları**
```lua
Config.Sickness.antibiotic = {
    cooldown = 1 * 60 * 60 * 1000,    -- Cooldown süresi (1 saat)
    reminderInterval = 30 * 60 * 1000, -- Hatırlatma aralığı (30 dakika)
    maxUses = 8                       -- Maksimum kullanım sayısı
}
```

## 🦠 Hastalık Sistemi

### **Enfeksiyon Kapma**
1. **Can düşer** - %80'in altına düşerse
2. **Uyarı alır** - "Hastalık riski! 3 dakika içinde tedavi olmalısın!"
3. **3 dakika bekler** - Can düşük kalırsa enfekte olur
4. **İlk evre başlar** - Hafif semptomlar

### **Evre 1 (0-15 dakika)**
- **Semptomlar:**
  - Hafif bulanıklık (0.2 yoğunluk)
  - 30 saniye aralıklarla öksürme
  - Normal yürüyüş
- **İyileşme:** 2 antibiyotik gerekli
- **Süre:** 15 dakika

### **Evre 2 (15+ dakika)**
- **Semptomlar:**
  - Şiddetli bulanıklık (0.8 yoğunluk)
  - 15 saniye aralıklarla öksürme
  - Sarhoş yürüyüş animasyonu
  - Gözler kapanıp açılıyor
- **İyileşme:** 6 antibiyotik gerekli
- **Süre:** Sınırsız (iyileşene kadar)

### **Bulaşma Sistemi**
- **Mesafe:** 2 metre yakındaki oyuncular
- **Şans:** %30 bulaşma olasılığı
- **Uyarı:** "Yakınında enfekte bir oyuncu var!"

## 💊 Antibiyotik Sistemi

### **Kullanım Kuralları**
- **Cooldown:** 1 saat bekleme süresi
- **Maksimum:** 8 kullanım hakkı
- **Hatırlatma:** 30 dakikada bir uyarı

### **Kullanım Süreci**
1. **Antibiyotik kullan** - Inventory'den tıkla
2. **Cooldown kontrolü** - 1 saat geçti mi?
3. **Animasyon** - Hap içme animasyonu
4. **Progress bar** - 5 saniye kullanım süresi
5. **Sonuç** - İyileşme veya devam

### **Evre Bazlı İyileşme**
- **Evre 1:** 2 antibiyotik = İyileşme
- **Evre 2:** 6 antibiyotik = İyileşme
- **Bilgilendirme:** "X tane daha kullanman gerekiyor"

### **Hatırlatma Mesajları**
- **Kullanma zamanı:** "Antibiyotik kullanma zamanın geldi!"
- **Bekleme:** "X dakika beklemen gerekiyor"
- **Maksimum:** "Maksimum kullanım sayısına ulaştın!"

## 👑 Admin Komutları

### **Temel Komutlar**
```bash
/infect [id]     # Oyuncuyu enfekte et
/heal [id]       # Oyuncuyu iyileştir
/sicklist        # Enfekte oyuncuları listele
```

### **Kullanım Örnekleri**
```bash
/infect 1        # ID 1 olan oyuncuyu enfekte et
/heal 1          # ID 1 olan oyuncuyu iyileştir
/sicklist        # Tüm enfekte oyuncuları göster
```

### **Yetki Gereksinimleri**
- `admin` veya `god` yetkisi gerekli
- QBCore permission sistemi kullanılıyor

## 🔧 API

### **Client Events**
```lua
-- Enfeksiyon kapma
RegisterNetEvent('hb_sicksystem:client:getInfected')

-- İyileşme
RegisterNetEvent('hb_sicksystem:client:healed')

-- Antibiyotik kullanma
RegisterNetEvent('hb_sicksystem:client:useAntibiotic')
```

### **Server Events**
```lua
-- Enfeksiyon kapma
TriggerServerEvent('hb_sicksystem:server:infectPlayer')

-- İyileşme
TriggerServerEvent('hb_sicksystem:server:healPlayer')

-- Antibiyotik kullanma
TriggerServerEvent('hb_sicksystem:server:useAntibiotic')
```

### **Export Fonksiyonları**
```lua
-- Oyuncunun hasta olup olmadığını kontrol et
local isSick = exports['hb_sicksystem']:IsPlayerSick(playerId)

-- Oyuncuyu enfekte et
exports['hb_sicksystem']:InfectPlayer(playerId)

-- Oyuncuyu iyileştir
exports['hb_sicksystem']:HealPlayer(playerId)
```

## 🎨 Özelleştirme

### **Animasyon Ayarları**
```lua
Config.Animations = {
    cough = {
        dict = "timetable@gardener@smoking_joint",
        anim = "idle_cough",
        flag = 49
    },
    pill = {
        dict = "mp_player_intdrink",
        anim = "loop_bottle",
        flag = 49,
        prop = "prop_cs_pills",
        bone = 18905,
        position = {x = 0.12, y = 0.028, z = 0.001},
        rotation = {x = 10.0, y = 175.0, z = 0.0}
    }
}
```

### **Eşya Ayarları**
```lua
Config.Items = {
    antibiotic = {
        name = "antibiotic",
        label = "Antibiotic",
        description = "Hastalığı tedavi eder"
    }
}
```

## 🐛 Sorun Giderme

### **Yaygın Sorunlar**

#### **1. Antibiyotik Çalışmıyor**
- ✅ Eşya `qb-core/shared/items.lua`'da tanımlı mı?
- ✅ `useable = true` ayarı var mı?
- ✅ Cooldown süresi geçti mi?

#### **2. Efektler Görünmüyor**
- ✅ Config.Debug = true yapın
- ✅ F8 konsolunu kontrol edin
- ✅ Script yeniden başlatıldı mı?

#### **3. Admin Komutları Çalışmıyor**
- ✅ QBCore permission sistemi aktif mi?
- ✅ `admin` veya `god` yetkiniz var mı?
- ✅ Komut syntax'ı doğru mu?

#### **4. Animasyonlar Çalışmıyor**
- ✅ Animasyon dictionary'leri yüklendi mi?
- ✅ Config.Animations ayarları doğru mu?
- ✅ FiveM cache temizlendi mi?

### **Debug Modu**
```lua
Config.Debug = true  -- config.lua'da aktif edin
```
Debug modu aktifken F8 konsolunda detaylı loglar görürsünüz.

## 📝 Changelog

### **v1.0.0**
- ✅ İlk sürüm
- ✅ Temel hastalık sistemi
- ✅ İki evreli hastalık
- ✅ Antibiyotik sistemi
- ✅ Admin komutları
- ✅ Görsel efektler

## 📄 Lisans

Bu proje MIT lisansı altında lisanslanmıştır. Detaylar için `LICENSE` dosyasına bakın.

**Hollyborn** - FiveM Script Developer

- Discord: [heydmrr]
- Discord: [hollystore]

**Not:** Bu script QBCore framework'ü gerektirir. Diğer framework'lerle uyumluluk garanti edilmez. 
