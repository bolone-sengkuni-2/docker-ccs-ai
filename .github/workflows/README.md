# GitHub Workflows untuk Docker Build

Repository ini memiliki 3 GitHub Actions workflows untuk automasi Docker build:

## 📋 Workflows yang Tersedia

### 1. `docker-build.yml` - Full Build & Push

**Trigger:**

- Push ke branch `main`, `master`, atau `develop`
- Push tag dengan format `v*` (contoh: `v1.0.0`)
- Pull request ke `main` atau `master`

**Fitur:**

- ✅ Build multi-platform (linux/amd64, linux/arm64)
- ✅ Push ke GitHub Container Registry (GHCR)
- ✅ Push ke Docker Hub
- ✅ Auto-tagging berdasarkan branch/tag
- ✅ Layer caching untuk build lebih cepat
- ✅ Support semantic versioning

**Output Images:**

- `ghcr.io/kaitranntt/ccs:latest`
- `ghcr.io/kaitranntt/ccs:<version>`
- `<dockerhub-username>/ccs:latest`
- `<dockerhub-username>/ccs:<version>`

---

### 2. `docker-hub.yml` - Docker Hub Only (Simplified)

**Trigger:**

- Push ke branch `main` atau `master`
- Mengabaikan perubahan pada file markdown dan docs

**Fitur:**

- ✅ Build multi-platform
- ✅ Push hanya ke Docker Hub
- ✅ Lebih sederhana dan cepat
- ✅ Tag dengan latest dan SHA

**Output Images:**

- `<dockerhub-username>/ccs:latest`
- `<dockerhub-username>/ccs:<git-sha>`

---

### 3. `docker-test.yml` - Build Testing

**Trigger:**

- Pull request ke branch utama
- Manual trigger (workflow_dispatch)

**Fitur:**

- ✅ Test build tanpa push
- ✅ Validasi Dockerfile
- ✅ Cepat (hanya build linux/amd64)

---

## 🔐 Setup Secrets

Untuk menggunakan workflows ini, tambahkan secrets berikut di repository settings:

### Untuk Docker Hub:

```
DOCKERHUB_USERNAME = username Docker Hub Anda
DOCKERHUB_TOKEN = Access token dari Docker Hub
```

**Cara membuat Docker Hub token:**

1. Login ke [Docker Hub](https://hub.docker.com)
2. Account Settings → Security → New Access Token
3. Copy token dan simpan sebagai secret

### Untuk GitHub Container Registry:

Tidak perlu setup! `GITHUB_TOKEN` sudah tersedia otomatis.

---

## 📝 Cara Menggunakan

### Build Otomatis

```bash
# Push ke main akan trigger build otomatis
git add .
git commit -m "Update code"
git push origin main
```

### Release dengan Versioning

```bash
# Tag commit dengan versi
git tag v1.0.0
git push origin v1.0.0

# Akan menghasilkan images:
# - ccs:latest
# - ccs:1.0.0
# - ccs:1.0
# - ccs:1
```

### Manual Build

Pergi ke **Actions** tab → Pilih workflow → **Run workflow**

---

## 🔍 Monitoring

**Lihat status build:**

1. Buka tab **Actions** di repository GitHub
2. Pilih workflow run yang ingin dilihat
3. Lihat detail logs untuk setiap step

**Badge Status:**
Tambahkan di README.md:

```markdown
![Docker Build](https://github.com/kaitranntt/ccs/actions/workflows/docker-build.yml/badge.svg)
```

---

## 🛠️ Customization

### Mengubah Platform Build

Edit file workflow, ubah line:

```yaml
platforms: linux/amd64,linux/arm64,linux/arm/v7
```

### Mengubah Trigger Conditions

Edit bagian `on:` di workflow:

```yaml
on:
  push:
    branches: [main, staging, production]
    paths:
      - "src/**"
      - "Dockerfile"
```

### Menambah Registry Lain

Tambahkan login step untuk registry lain:

```yaml
- name: Login to Custom Registry
  uses: docker/login-action@v3
  with:
    registry: registry.example.com
    username: ${{ secrets.REGISTRY_USERNAME }}
    password: ${{ secrets.REGISTRY_PASSWORD }}
```

---

## 🚀 Tips Optimasi

1. **Gunakan BuildKit caching** - Sudah diaktifkan dengan `cache-from: type=gha`
2. **Multi-stage builds** - Gunakan di Dockerfile untuk image lebih kecil
3. **Ignore unnecessary files** - Tambahkan `.dockerignore`
4. **Path filters** - Hanya build jika file penting berubah

---

## 📚 Referensi

- [Docker Build Push Action](https://github.com/docker/build-push-action)
- [Docker Login Action](https://github.com/docker/login-action)
- [GitHub Actions Documentation](https://docs.github.com/actions)
