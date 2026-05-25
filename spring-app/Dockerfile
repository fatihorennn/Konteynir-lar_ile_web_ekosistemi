# ==========================================
# 1. AŞAMA: Uygulamanın Derlenmesi (Build Stage)
# ==========================================
FROM maven:3.9.6-eclipse-temurin-21-alpine AS builder

WORKDIR /app

# Maven bağımlılıklarını önbelleğe (cache) almak için önce sadece pom.xml'i kopyalıyoruz.
# Bu sayede kod değişse bile bağımlılıklar tekrar tekrar indirilmez, build süresi kısalır.
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Kaynak kodları kopyalıyoruz
COPY src ./src

# Uygulamayı derleyip testleri atlayarak jar paketini oluşturuyoruz
RUN mvn package -DskipTests

# ==========================================
# 2. AŞAMA: Uygulamanın Çalıştırılması (Run Stage)
# ==========================================
FROM eclipse-temurin:21-jre-alpine

WORKDIR /app

# Güvenlik Önlemi: Uygulamayı root yetkileriyle çalıştırmamak için sistem kullanıcısı açıyoruz
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring

# İlk aşamada üretilen .jar dosyasını bu hafif imaja kopyalıyoruz
COPY --from=builder /app/target/*.jar app.jar

# Spring Boot varsayılan olarak 8080 portunu kullanır
EXPOSE 8080

# Uygulamayı optimize edilmiş JVM parametreleriyle başlatıyoruz
ENTRYPOINT ["java", "-jar", "app.jar"]