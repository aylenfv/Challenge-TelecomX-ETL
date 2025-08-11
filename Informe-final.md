# Informe Final — Análisis de Evasión de Clientes (Churn)  
**Proyecto:** Telecom X — Churn Analysis  
**Autor:** Aylen Fissore Vergara (analista)  
**Programa:** ONE + Alura LATAM
**Especialización**: Data Science
**Fecha:** 10/08/2025

---

## 1. Problema a analizar
Telecom X enfrenta una tasa significativa de cancelaciones (churn) y necesita identificar los factores que más influyen en la pérdida de clientes. El objetivo es entender qué variables (perfil del cliente, tipo de servicio, suscripciones y facturación) están asociadas a la evasión para poder proponer medidas de retención.

---

## 2. Objetivo
- Cuantificar la evasión de clientes y su distribución por segmentos relevantes.  
- Detectar patrones y variables con mayor asociación a churn.  
- Proponer recomendaciones de negocio para reducir la tasa de cancelación.

---

## 3. Resumen ejecutivo (principales cifras)
- **Total registros analizados:** 7.032 clientes (1.869 dieron de baja; 5.163 siguen activos). 

- **Tasa global de churn:** ≈ **26.6%** (1.869 / 7.032).  
- Churn por género: **Female 26.96%**, **Male 26.21%**.  
- Etiquetas de alto interés: clientes **SeniorCitizen** presentan mayor churn: **>65 años 41.68%** vs **<=65 años 23.65%**.  
- Clientes con **contratos mes a mes** y con **pago por cheque electrónico** presentan tasas de churn mucho más altas.

---

## 4. Metodología
1. **Extracción:** lectura del JSON original y normalización (se generó un DataFrame aplanado con columnas tipo `customer.*`, `phone.*`, `internet.*`, `account.*`).  
2. **Transformación / Limpieza:** tratamiento de nulos, conversión de tipos (ej.: `account.Charges.Total` a flotante), generación de columnas derivadas (ej.: `account.Charges.Daily`).  
3. **Análisis exploratorio (EDA):**  
   - Estadísticas descriptivas (media, mediana, desviación estándar) en variables numéricas: `customer.tenure`, `account.Charges.Monthly`, `account.Charges.Total`, `account.Charges.Daily`.  
   - Análisis de proporciones para variables categóricas (p. ej. churn por `Contract`, `PaymentMethod`, `InternetService`).  
4. **Visualización:** boxplots, scatter plots, líneas de tendencia y gráficos de barras apiladas para comparar proporciones entre grupos.

---

## 5. Principales Hallazgos

### 5.1. Perfil del cliente (demográficos)
- **Género:** tasas similares (Female 26.96% vs Male 26.21%). No se observa una diferencia práctica relevante.  
  ![Relación entre género y churn](../assets/churn-by-gender.png)
- **Senior Citizen:** fuerte diferencia — los clientes mayores (SeniorCitizen = 'Yes') presentan **~41.7%** de churn frente a **~23.6%** en no-seniors. Esto sugiere mayor riesgo en la población mayor.  
  ![Relación entre personas mayores y churn](../assets/churn-by-seniorCitizen.png)
- **Partner / Dependents:**  
  - `Partner = No` → churn ≈ **32.98%**; `Partner = Yes` → **19.72%**.  
  ![Relación entre personas con/sin pareja y churn](../assets/churn-by-partner.png)
  - `Dependents = No` → churn ≈ **31.28%**; `Dependents = Yes` → **15.53%**.  
  ![Relación entre personas con/sin dependientes y churn](../assets/churn-by-dependents.png)
  => Los clientes sin pareja y sin dependientes tienen tasas de churn más altas; su perfil parece más propenso a abandonar.

### 5.2. Evasión asociada a suscripciones (servicios)
- **Tipo de Internet:** `Fiber optic` muestra **alta** evasión con ~**41.9%**, `DSL` ~**19.0%**, `No internet` ~**7.43%**.  
![Relación entre tipo de internet y churn](../assets/churn-by-internetServiceType.png)
- **Servicios de valor agregado (OnlineSecurity, OnlineBackup, DeviceProtection, TechSupport):** ausencia de estas suscripciones está asociada a churn mucho más alto (ej.: `OnlineSecurity = No` → **41.78%** vs `Yes` → **14.64%**).  
![Relación entre clientes con/sin suscripción a Online Security y churn](../assets/churn-by-onlineSecuritySubscription.png)
![Relación entre clientes con/sin suscripción a Online Backup y churn](../assets/churn-by-onlineBackupSubscription.png)
![Relación entre clientes con/sin plan Device Protection y churn](../assets/churn-by-deviceProtectionPlan.png)
![Relación entre clientes con/sin Tech Support y churn](../assets/churn-by-techSupportSubscription.png)
  => Los clientes que no tienen servicios complementarios son mucho más propensos a irse; los bundles (paquetes de productos) o upsells (mejorar el servicio que el cliente desea, agregando nuevas características) podrían ayudar a retener.

### 5.3. Evasión asociada a facturación
- **Monthly Charges:** boxplot de `account.Charges.Monthly` por estado muestra que **los churned tienden a tener cargos mensuales más altos** (medianas y cuartiles superiores comparados con activos).  
  **Interpretación:** pagos mensuales altos están relacionados con abandono, especialmente en clientes nuevos (ver abajo).

- **Tenure (tiempo de permanencia):** la tasa de churn es **altísima en los primeros meses** (mes 1 ~61.99%, mes 2 ~51.68%, mes 3 ~47.0% …) y disminuye con el tiempo; con tenure avanzado (< ~20 meses) la probabilidad decae notablemente.    
  **Interpretación:** la ventana crítica de riesgo es el **periodo inicial** (primeros 6-12 meses). Acción temprana es clave.

- **Tenure vs Monthly Charges (combinado):** clientes **nuevos con cargos altos** muestran la mayor concentración de churn (puntos rojos concentrados en la esquina baja-tenure/alto-charge).  

### 5.4. Otras observaciones relevantes
- **Contract type:** `Month-to-month` → churn **~42.71%**, `One year` ~11.28%, `Two year` ~2.85%. Contratos a largo plazo muestran retención mucho mejor.  
  ![Churn por tipo de contrato](../assets/churn-by-contractType.png)
- **Payment Method:** `Electronic check` tiene churn muy alto (~45.29%), mientras que pagos automáticos (bank transfer / credit card auto) muestran bajas tasas (~16–17%).  
  ![Churn por forma de pago](../assets/churn-by-paymentMethod.png)
- **Daily Charges:** parece haber variabilidad por valores diarios, pero la interpretación requiere agrupar o discretizar mejor para sacar conclusiones robustas.
  ![Churn por cargos diarios](../assets/churn-by-dailyCharges.png)

---

## 6. Visualizaciones clave (y análisis detallado)

1. **Boxplot: Churn by Monthly Charges**  
   ![Relación entre cargos mensuales y churn](../assets/churn-by-monthlyCharges.png)  
   - Análisis: los usuarios churned muestran mayor mediana y cuartil superior en cargos mensuales; sugiere sensibilidad al precio (o a la relación precio/valor).

2. **Línea: Churn Rate by Tenure**  
   ![Churn Rate by Customer Tenure](../assets/churn-by-tenure-line.png) 
   - Análisis: curva decreciente de churn con el tiempo. Primera fase (mes 1–6) es crítica.

3. **Scatter: Tenure vs Monthly Charges (color por Churn)**  
   ![Churn por Tenure vs Monthly Charges](../assets/churn-by-tenure-vs-monthlyCharges.png)  
   - Análisis: alta densidad de churn en combinación de **baja permanencia** y **alto cargo mensual**.

---

## 7. Variables correlativas (resumen)
- **Fuertes asociaciones con churn:** `Contract (Month-to-month)`, `PaymentMethod (Electronic check)`, `InternetService (Fiber)`, ausencia de `OnlineSecurity/Backup/TechSupport/DeviceProtection`, `SeniorCitizen = Yes`, `MonthlyCharges` (valores altos), **baja permanencia**.  
- **Débil o nula asociación:** género (valores similares), algunas combinaciones de `phone` services muestran poca diferencia práctica.

---

## 8. Recomendaciones (priorizadas)
1. **Programas de incorporación y ofertas para nuevos clientes:**  
   - Ofrecer descuento inicial o trial ampliado en los primeros 1–3 meses.  
   - Paquetes de “Bienvenida” que bajen el cargo mensual al inicio.

2. **Incentivar contratos a plazo:**  
   - Promociones que recompensen el paso a contratos 1 o 2 años (descuentos, servicios incluidos).

3. **Fomentar pagos automáticos:**  
   - Incentivos para cambiar de `Electronic check` a métodos automáticos (bank transfer/credit card automatic), dado su menor churn.

4. **Paquetes de servicios / Agregado de seguridad y soporte:**  
   - Ofrecer paquetes con `OnlineSecurity`, `TechSupport`, `DeviceProtection` ya en la incorporación del nuevo cliente a la empresa; los clientes con esos servicios presentan mucha menos evasión.

5. **Segmentación específica para senior customers:**  
   - Diseñar ofertas adaptadas a `SeniorCitizen`, con servicios/propuestas de valor que mejoren la percepción de coste/beneficio.

6. **Alertas y campañas proactivas:**  
   - Detectar clientes en riesgo (baja permanencia + cargos mensuales altos) y realizar campañas de retención proactivas (ofertas, llamadas de atención al cliente).

---

## 9. Limitaciones del análisis
- Los hallazgos son descriptivos: **correlación ≠ causalidad**. Para causalidad se requieren experimentos o estudios adicionales.  
- Algunas variables continuas (ej. `DailyCharges`) requieren discretización para interpretar mejor.  
- El análisis no consideró interacciones complejas (podría haber grupos con comportamiento distinto por combinación de variables).

---

## 10. Siguientes pasos recomendados (técnicos)
- Entrenar un **modelo predictivo (regresión logística / random forest)** para identificar variables con mayor poder explicativo y producir un score de riesgo por cliente.  
- Calcular **importancias de variable (feature importance / SHAP)** para priorizar acciones.  
- Probar **A/B tests** con las recomendaciones (oferta de onboarding, métodos de pago incentivados) y medir impacto en churn.

---

## 11. Anexos
- **Datos duros**:  
  - Registros: 7.032 (5.163 activos, 1.869 churned).  
  - Listado de churn rates por variable (agregado) — (incluir tabla exportada desde notebook si la tienes).  
- **Gráficos generados:**  
  - `churn-by-monthlyCharges.png`  
  - `churn-by-tenure-line.png`  
  - `churn-by-tenure-vs-monthlyCharges.png`


