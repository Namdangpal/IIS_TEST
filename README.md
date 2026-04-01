# IIS_TEST
<configuration>
  <system.webServer>
    <rewrite>
      <rules>

        <!-- ========================= -->
        <!-- 1. Chrome → Modern Rewrite -->
        <!-- ========================= -->
        <rule name="Chrome to Modern (with file check)" stopProcessing="true">
          <match url="^(.*)\.(asp|js|css|png|jpg|gif|svg)$" ignoreCase="true" />

          <conditions logicalGrouping="MatchAll">

            <!-- 1. 크롬 브라우저 -->
            <add input="{HTTP_USER_AGENT}" pattern="Chrome|Edg" />

            <!-- 2. 이미 modern 경로면 제외 -->
            <add input="{REQUEST_URI}" pattern="^/modern/" negate="true" />

            <!-- 3. modern 경로에 파일이 실제 존재할 때만 -->
            <add input="{APPL_PHYSICAL_PATH}modern\{R:1}.{R:2}" matchType="IsFile" />

          </conditions>

          <!-- 내부 rewrite -->
          <action type="Rewrite" url="/modern/{R:1}.{R:2}" />
        </rule>


        <!-- ========================= -->
        <!-- 2. 기본 fallback (아무것도 안하면 원래 경로 유지) -->
        <!-- ========================= -->
        <!-- 별도 rule 필요 없음 -->


      </rules>
    </rewrite>

    <!-- ========================= -->
    <!-- 3. 캐시 문제 해결 (강제 갱신) -->
    <!-- ========================= -->
    <httpProtocol>
      <customHeaders>
        <add name="Cache-Control" value="no-cache, no-store, must-revalidate" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="0" />
      </customHeaders>
    </httpProtocol>

  </system.webServer>
</configuration>
