# ============================
# Stage 1: 构建阶段 (Builder)
# ============================
# 使用 Maven 作为基础镜像以便构建应用程序, 并将此阶段命名为 build
FROM maven:3.9-eclipse-temurin-17 AS build
# 工作目录
WORKDIR /build

# 单独复制 pom.xml 到当前位置`.` (`.`即为工作目录`/build`)
# 这一步利用 Docker 缓存：只要 pom.xml 没变，就不需要重新下载依赖
COPY pom.xml .
# 下载依赖项，参数 go-offline: 即使在离线状态下，也可以下载所有依赖项。
RUN mvn dependency:go-offline

# 复制源代码并构建, 将后端的src文件夹复制到/build下的src
COPY src ./src
# -DskipTests：构建时跳过测试
RUN mvn clean package -DskipTests

# ============================
# Stage 2: 运行阶段 (Runtime)
# ============================
# 运行Java应用程序需要JDK
FROM amazoncorretto:17

# 定义几件事
# arguments
ARG PROFILE=dev
ARG APP_VERSION=1.0.0

# 工作目录
WORKDIR /app

# 将构建阶段工作目录中的 jar 文件复制到本阶段的工作目录中，体现第一阶段 FROM 时 AS build 的一个作用
COPY --from=build /build/target/book-network-*.jar /app/app.jar

# 暴露端口
EXPOSE 8088

# environment
ENV DB_URL=jdbc:postgresql://postgres-sql-bsn:5432/book_social_network
ENV KEYCLOAK_URL=http://keycloak:8080/realms/book-social-network
ENV ACTIVE_PROFILE=${PROFILE}
# ENV JAR_VERSION=${APP_VERSION}

# CMD ["java -jar -Dspring.profiles.active=$ACTIVE_PROFILE -Dspring.datasource.url=$DB_URL book-network-$JAR_VERSION.jar"]
CMD java -jar -Dspring.profiles.active=${ACTIVE_PROFILE} -Dspring.datasource.url=${DB_URL} app.jar
#CMD ["java", \
#     "-jar", \
#     "-Dspring.profiles.active=${ACTIVE_PROFILE}", \
#     "-Dspring.datasource.url=${DB_URL}", \
#     "book-network-${JAR_VERSION}.jar"]








