# PC3_ClaudiaYesenia

Repositorio con el análisis completo de:
- Reservas internacionales
- Consumo energético per cápita
- Capacidad eléctrica instalada
- Gasto educativo (% PIB)

## Contenido del Repositorio

1. `Analisis_Indicadores.ipynb`: Notebook de Colab con todo el procesamiento
2. `dataset_final_con_categorias.csv`: Dataset principal con categorías
3. `estadisticos_por_region.csv`: Resumen estadístico por región
4. `index.html`: Página web explicativa

## Página Web Explicativa
[Ver página web](PC3_ClaudiaYesenia.html)

## Página web del proyecto
[Ver página web](https://github.com/ClaudiaPomahuallca/PC3_ClaudiaYesenia.git)

## Cómo replicar el análisis
1. Clonar el repositorio
2. Abrir el notebook en Google Colab
3. Ejecutar las celdas en orden

## Resultados Clave
- Clasificación de países en 5 categorías para cada indicador
- Comparativa regional de los indicadores
"""


```python
import pandas as pd
from tabulate import tabulate

# Cargar los datos
reserves = pd.read_csv('https://github.com/ClaudiaPomahuallca/PC3_ClaudiaYesenia/raw/refs/heads/main/Reserves%20of%20foreign%20exchange%20and%20gold.csv')
energy = pd.read_csv('https://github.com/ClaudiaPomahuallca/PC3_ClaudiaYesenia/raw/refs/heads/main/Energy%20consumption%20per%20capita.csv')
electricity = pd.read_csv('https://github.com/ClaudiaPomahuallca/PC3_ClaudiaYesenia/raw/refs/heads/main/Electricity%20-%20installed%20generating%20capacity.csv')
education = pd.read_csv('https://github.com/ClaudiaPomahuallca/PC3_ClaudiaYesenia/raw/refs/heads/main/Education%20expenditures.csv')

# Función para procesar y limpiar datos
def process_df(df, value_col, new_name):
    df = df[['name', 'region', 'date_of_information', value_col]].copy()
    df.columns = ['country', 'region', 'date', new_name]
    df[new_name] = pd.to_numeric(
        df[new_name].astype(str).str.replace('[^\d.]', '', regex=True),
        errors='coerce'
    )
    return df.dropna(subset=[new_name])
# Procesar cada dataset
reserves_clean = process_df(reserves, 'value', 'reserves_usd')
energy_clean = process_df(energy, ' Btu/person', 'energy_btu_per_person')
electricity_clean = process_df(electricity, ' kW', 'electricity_kw')
education_clean = process_df(education, '% of GDP', 'education_pct_gdp')
# Combinar todos los datos ignorando fechas diferentes
def combine_data(*dfs):
    combined = pd.concat(dfs)
    # Para cada variable, tomar el valor más reciente no nulo por país
    combined = combined.groupby(['country', 'region']).agg({
        'reserves_usd': 'last',
        'energy_btu_per_person': 'last',
        'electricity_kw': 'last',
        'education_pct_gdp': 'last'
    }).reset_index()
    # Eliminar países que no tienen los 4 valores completos
    combined = combined.dropna()
    return combined

final_df = combine_data(reserves_clean, energy_clean, electricity_clean, education_clean)
# Configuración para mostrar toda la tabla
pd.set_option('display.max_rows', None)
pd.set_option('display.max_columns', None)
pd.set_option('display.width', 1000)

# Mostrar tabla formateada
print(" DATASET FINAL CONSOLIDADO POR PAÍS")
print(tabulate(
    final_df,
    headers='keys',
    tablefmt='psql',
    showindex=False,
    floatfmt=".1f"
))

# Guardar a CSV
final_df.to_csv('dataset_final_consolidado.csv', index=False)
print("\n Datos guardados en 'dataset_final_consolidado.csv'")
 DATASET FINAL CONSOLIDADO POR PAÍS
+-----------------------------------+-----------------------------------+-----------------+-------------------------+------------------+---------------------+
| country                           | region                            |    reserves_usd |   energy_btu_per_person |   electricity_kw |   education_pct_gdp |
|-----------------------------------+-----------------------------------+-----------------+-------------------------+------------------+---------------------|
| Afghanistan                       | South Asia                        |    9749000000.0 |               3380000.0 |         627000.0 |                 2.9 |
| Albania                           | Europe                            |    6455000000.0 |              27407000.0 |        2857000.0 |                 3.1 |
| Algeria                           | Africa                            |   68448000000.0 |              61843000.0 |       22591000.0 |                 7.0 |
| Angola                            | Africa                            |   14243000000.0 |               9146000.0 |        7600000.0 |                 2.4 |
| Antigua and Barbuda               | Central America and the Caribbean |     358441000.0 |             110114000.0 |         148000.0 |                 3.8 |
| Argentina                         | South America                     |   24381000000.0 |              78496000.0 |       47631000.0 |                 5.0 |
| Armenia                           | Central Asia                      |    3684000000.0 |              54689000.0 |        4265000.0 |                 2.8 |
| Aruba                             | Central America and the Caribbean |    1468000000.0 |             153952000.0 |         305000.0 |                 5.5 |
| Australia                         | Australia and Oceania             |   54455000000.0 |             223158000.0 |      108193000.0 |                 6.1 |
| Austria                           | Europe                            |   11919000000.0 |             120211000.0 |       33371000.0 |                 5.1 |
| Azerbaijan                        | Central Asia                      |   12699000000.0 |              66467000.0 |        8383000.0 |                 4.3 |
| Bahamas, The                      | Central America and the Caribbean |    2512000000.0 |             104409000.0 |         608000.0 |                 2.8 |
| Bahrain                           | Middle East                       |    5118000000.0 |             554202000.0 |        7031000.0 |                 2.2 |
| Bangladesh                        | South Asia                        |   20225000000.0 |              11472000.0 |       22699000.0 |                 2.1 |
| Barbados                          | Central America and the Caribbean |    1606000000.0 |              68293000.0 |         320000.0 |                 6.5 |
| Belarus                           | Europe                            |    4392000000.0 |             104821000.0 |       12653000.0 |                 4.7 |
| Belgium                           | Europe                            |   22374000000.0 |             190416000.0 |       28248000.0 |                 6.7 |
| Belize                            | Central America and the Caribbean |     498087000.0 |              30752000.0 |         220000.0 |                 8.7 |
| Benin                             | Africa                            |     698900000.0 |               6472000.0 |         505000.0 |                 3.0 |
| Bhutan                            | South Asia                        |     928229000.0 |              64082000.0 |        2344000.0 |                 7.0 |
| Bolivia                           | South America                     |      87100000.0 |              29340000.0 |        4375000.0 |                 9.8 |
| Botswana                          | Africa                            |    4756000000.0 |              32443000.0 |         758000.0 |                 8.7 |
| Brazil                            | South America                     |  318857000000.0 |              48889000.0 |      240251000.0 |                 6.0 |
| Brunei                            | East and Southeast Asia           |    4032000000.0 |             403365000.0 |         904000.0 |                 4.4 |
| Bulgaria                          | Europe                            |   40270000000.0 |             102171000.0 |       12939000.0 |                 4.0 |
| Burkina Faso                      | Africa                            |      49000000.0 |               3481000.0 |         749000.0 |                 5.5 |
| Burma                             | East and Southeast Asia           |    9338000000.0 |               8384000.0 |        7419000.0 |                 2.1 |
| Burundi                           | Africa                            |      90350000.0 |                946000.0 |         131000.0 |                 5.0 |
| Cabo Verde                        | Africa                            |     783106000.0 |              19999000.0 |         200000.0 |                 7.6 |
| Cambodia                          | East and Southeast Asia           |   18608000000.0 |              15664000.0 |        3673000.0 |                 3.1 |
| Cameroon                          | Africa                            |    4882000000.0 |               4271000.0 |        1798000.0 |                 3.2 |
| Canada                            | North America                     |  117551000000.0 |             311599000.0 |      161988000.0 |                 5.2 |
| Cayman Islands                    | Central America and the Caribbean |     234000000.0 |             143149000.0 |         176000.0 |                 2.1 |
| Central African Republic          | Africa                            |     479593000.0 |                954000.0 |          63000.0 |                 2.2 |
| Chad                              | Africa                            |    1050000000.0 |               1502000.0 |         167000.0 |                 2.9 |
| Chile                             | South America                     |   44382000000.0 |              71420000.0 |       39238000.0 |                 5.6 |
| China                             | East and Southeast Asia           | 3265000000000.0 |             113805000.0 |     2949000000.0 |                 3.6 |
| Colombia                          | South America                     |   61506000000.0 |              29305000.0 |       21053000.0 |                 4.9 |
| Comoros                           | Africa                            |     322436000.0 |               7139000.0 |          32000.0 |                 2.6 |
| Congo, Democratic Republic of the | Africa                            |    5104000000.0 |               1305000.0 |        3229000.0 |                 2.7 |
| Congo, Republic of the            | Africa                            |     715391000.0 |               7351000.0 |         842000.0 |                 4.5 |
| Costa Rica                        | Central America and the Caribbean |   14177000000.0 |              30725000.0 |        3751000.0 |                 6.7 |
| Cote d'Ivoire                     | Africa                            |    6257000000.0 |               8489000.0 |        2315000.0 |                 3.4 |
| Croatia                           | Europe                            |    3336000000.0 |              79907000.0 |        5518000.0 |                 5.5 |
| Cyprus                            | Europe                            |     921269000.0 |             107188000.0 |        2288000.0 |                 6.1 |
| Czechia                           | Europe                            |  141988000000.0 |             136306000.0 |       21802000.0 |                 5.1 |
| Denmark                           | Europe                            |  103239000000.0 |              98513000.0 |       20794000.0 |                 6.4 |
| Djibouti                          | Africa                            |     348725000.0 |              10428000.0 |         210000.0 |                 3.6 |
| Dominica                          | Central America and the Caribbean |     155971000.0 |              36395000.0 |          41000.0 |                 5.5 |
| Dominican Republic                | Central America and the Caribbean |   13424000000.0 |              39329000.0 |        6581000.0 |                 4.6 |
| Ecuador                           | South America                     |    4703000000.0 |              35700000.0 |        8438000.0 |                 3.7 |
| Egypt                             | Africa                            |   34278000000.0 |              34975000.0 |       59680000.0 |                 2.5 |
| El Salvador                       | Central America and the Caribbean |    3590000000.0 |              24421000.0 |        2803000.0 |                 4.1 |
| Estonia                           | Europe                            |    2054000000.0 |              73679000.0 |        3225000.0 |                 6.6 |
| Eswatini                          | Africa                            |     479261000.0 |              18823000.0 |         285000.0 |                 5.0 |
| Ethiopia                          | Africa                            |    3784000000.0 |               2366000.0 |        5690000.0 |                 4.5 |
| Fiji                              | Australia and Oceania             |    1597000000.0 |              25375000.0 |         427000.0 |                 5.7 |
| Finland                           | Europe                            |   14322000000.0 |             183540000.0 |       26782000.0 |                 5.9 |
| France                            | Europe                            |   78429000000.0 |             123526000.0 |      151463000.0 |                 5.5 |
| Gabon                             | Africa                            |    1447000000.0 |              22101000.0 |         785000.0 |                 3.2 |
| Gambia, The                       | Africa                            |     577028000.0 |               2731000.0 |         162000.0 |                 2.8 |
| Gaza Strip                        | Middle East                       |    1328000000.0 |              14991000.0 |         352000.0 |                 5.3 |
| Georgia                           | Central Asia                      |    3848000000.0 |              56076000.0 |        4526000.0 |                 3.6 |
| Germany                           | Europe                            |   96792000000.0 |             120457000.0 |      275658000.0 |                 4.7 |
| Ghana                             | Africa                            |    3624000000.0 |              10493000.0 |        5519000.0 |                 3.9 |
| Greece                            | Europe                            |    5610000000.0 |              92693000.0 |       24169000.0 |                 4.4 |
| Grenada                           | Central America and the Caribbean |     423263000.0 |              41703000.0 |          60000.0 |                 3.6 |
| Guatemala                         | Central America and the Caribbean |   23834000000.0 |              17096000.0 |        4995000.0 |                 3.1 |
| Guinea                            | Africa                            |    1887000000.0 |               5235000.0 |        1060000.0 |                 2.2 |
| Guinea-Bissau                     | Africa                            |     356400000.0 |               2351000.0 |          29000.0 |                 2.7 |
| Guyana                            | South America                     |    1010000000.0 |              46045000.0 |         259000.0 |                 4.5 |
| Haiti                             | Central America and the Caribbean |    2566000000.0 |               3486000.0 |         472000.0 |                 1.4 |
| Honduras                          | Central America and the Caribbean |    7543000000.0 |              16642000.0 |        3334000.0 |                 6.4 |
| Hong Kong                         | East and Southeast Asia           |  425554000000.0 |             116811000.0 |       13300000.0 |                 4.0 |
| Hungary                           | Europe                            |   37194000000.0 |              96152000.0 |       14829000.0 |                 4.8 |
| India                             | South Asia                        |  569544000000.0 |              25179000.0 |      499136000.0 |                 4.5 |
| Indonesia                         | East and Southeast Asia           |  149118000000.0 |              37390000.0 |       70826000.0 |                 3.5 |
| Iran                              | Middle East                       |  120600000000.0 |             160779000.0 |       86058000.0 |                 3.6 |
| Iraq                              | Middle East                       |   87048000000.0 |              64311000.0 |       31339000.0 |                 4.7 |
| Ireland                           | Europe                            |   11688000000.0 |             113837000.0 |       12321000.0 |                 3.1 |
| Israel                            | Middle East                       |  214544000000.0 |             112437000.0 |       22612000.0 |                 7.1 |
| Italy                             | Europe                            |   84875000000.0 |              96797000.0 |      128692000.0 |                 4.3 |
| Jamaica                           | Central America and the Caribbean |    4869000000.0 |              42095000.0 |        1242000.0 |                 6.0 |
| Japan                             | East and Southeast Asia           | 1160000000000.0 |             129504000.0 |      361617000.0 |                 3.4 |
| Jordan                            | Middle East                       |   15929000000.0 |              32909000.0 |        6891000.0 |                 3.2 |
| Kazakhstan                        | Central Asia                      |   21980000000.0 |             172936000.0 |       27624000.0 |                 4.5 |
| Kenya                             | Africa                            |   10065000000.0 |               5486000.0 |        3824000.0 |                 4.8 |
| Korea, South                      | East and Southeast Asia           |  409457000000.0 |             234668000.0 |      151139000.0 |                 4.7 |
| Kuwait                            | Middle East                       |   44104000000.0 |             389848000.0 |       20294000.0 |                 6.6 |
| Kyrgyzstan                        | Central Asia                      |    3237000000.0 |              27580000.0 |        3944000.0 |                 6.2 |
| Laos                              | East and Southeast Asia           |    1770000000.0 |              34463000.0 |       12738000.0 |                 2.3 |
| Latvia                            | Europe                            |    4591000000.0 |              65908000.0 |        3428000.0 |                 6.0 |
| Lebanon                           | Middle East                       |    9240000000.0 |              43105000.0 |        5161000.0 |                 1.7 |
| Lesotho                           | Africa                            |     854089000.0 |               8117000.0 |         104000.0 |                 8.7 |
| Liberia                           | Africa                            |     599660000.0 |               1822000.0 |         199000.0 |                 2.7 |
| Lithuania                         | Europe                            |    6920000000.0 |              83700000.0 |        5426000.0 |                 4.0 |
| Luxembourg                        | Europe                            |    2602000000.0 |             224651000.0 |        2212000.0 |                 5.0 |
| Macau                             | East and Southeast Asia           |   29392000000.0 |              64641000.0 |         427000.0 |                 6.3 |
| Madagascar                        | Africa                            |    2785000000.0 |               1816000.0 |         759000.0 |                 3.1 |
| Malawi                            | Africa                            |     594498000.0 |               1792000.0 |         731000.0 |                 2.9 |
| Malaysia                          | East and Southeast Asia           |  112968000000.0 |             113163000.0 |       37220000.0 |                 3.9 |
| Maldives                          | South Asia                        |     673203000.0 |              50886000.0 |         432000.0 |                 5.8 |
| Mali                              | Africa                            |     647800000.0 |               4307000.0 |        1222000.0 |                 4.4 |
| Malta                             | Europe                            |    1402000000.0 |             234698000.0 |         829000.0 |                 5.9 |
| Mauritania                        | Africa                            |    2039000000.0 |              14135000.0 |         812000.0 |                 1.9 |
| Mauritius                         | Africa                            |    7465000000.0 |              60188000.0 |         955000.0 |                 4.9 |
| Mexico                            | North America                     |  221944000000.0 |              57539000.0 |      105586000.0 |                 4.3 |
| Moldova                           | Europe                            |    5477000000.0 |              35686000.0 |         779000.0 |                 6.4 |
| Mongolia                          | East and Southeast Asia           |    4891000000.0 |              67132000.0 |        1510000.0 |                 4.7 |
| Morocco                           | Africa                            |   35269000000.0 |              23520000.0 |       14615000.0 |                 6.8 |
| Mozambique                        | Africa                            |    3513000000.0 |               5789000.0 |        2860000.0 |                 6.3 |
| Namibia                           | Africa                            |    3356000000.0 |              21734000.0 |         646000.0 |                 9.6 |
| Nepal                             | South Asia                        |   12456000000.0 |               6604000.0 |        2853000.0 |                 4.2 |
| Netherlands                       | Europe                            |   27753000000.0 |             185536000.0 |       59982000.0 |                 5.3 |
| New Zealand                       | Australia and Oceania             |   22065000000.0 |             121647000.0 |       10643000.0 |                 6.0 |
| Nicaragua                         | Central America and the Caribbean |    6105000000.0 |              10660000.0 |        1849000.0 |                 4.6 |
| Niger                             | Africa                            |    1314000000.0 |               1772000.0 |         377000.0 |                 3.8 |
| Nigeria                           | Africa                            |   38612000000.0 |               7993000.0 |        4094000.0 |                 0.5 |
| Norway                            | Europe                            |   81242000000.0 |             206961000.0 |       41100000.0 |                 5.9 |
| Oman                              | Middle East                       |   17298000000.0 |             296586000.0 |       11589000.0 |                 5.4 |
| Pakistan                          | South Asia                        |   12977000000.0 |              14076000.0 |       43512000.0 |                 2.4 |
| Panama                            | Central America and the Caribbean |    6856000000.0 |              78010000.0 |        4485000.0 |                 3.9 |
| Papua New Guinea                  | East and Southeast Asia           |    3901000000.0 |               8781000.0 |        1148000.0 |                 1.4 |
| Paraguay                          | South America                     |    9886000000.0 |              25733000.0 |        8928000.0 |                 3.3 |
| Peru                              | South America                     |   76338000000.0 |              30923000.0 |       16164000.0 |                 4.0 |
| Philippines                       | East and Southeast Asia           |   95251000000.0 |              17654000.0 |       29174000.0 |                 3.7 |
| Poland                            | Europe                            |  185515000000.0 |             103651000.0 |       64806000.0 |                 5.2 |
| Portugal                          | Europe                            |   10332000000.0 |              73285000.0 |       25409000.0 |                 5.0 |
| Qatar                             | Middle East                       |   44692000000.0 |             814308000.0 |       11400000.0 |                 3.2 |
| Romania                           | Europe                            |   64699000000.0 |              59377000.0 |       19748000.0 |                 3.7 |
| Russia                            | Central Asia                      |  597217000000.0 |             224858000.0 |      301926000.0 |                 3.7 |
| Rwanda                            | Africa                            |    2406000000.0 |               1808000.0 |         294000.0 |                 3.8 |
| Saint Kitts and Nevis             | Central America and the Caribbean |     294748000.0 |              81454000.0 |          72000.0 |                 2.5 |
| Saint Lucia                       | Central America and the Caribbean |     406064000.0 |              47522000.0 |          93000.0 |                 3.6 |
| Saint Vincent and the Grenadines  | Central America and the Caribbean |     316824000.0 |              37253000.0 |          55000.0 |                 5.7 |
| Samoa                             | Australia and Oceania             |     507740000.0 |              23476000.0 |          54000.0 |                 4.8 |
| Sao Tome and Principe             | Africa                            |      46247000.0 |               8875000.0 |          29000.0 |                 5.0 |
| Saudi Arabia                      | Middle East                       |  436769000000.0 |             349692000.0 |      119620000.0 |                 7.8 |
| Senegal                           | Africa                            |    1827000000.0 |               8303000.0 |        1772000.0 |                 5.5 |
| Serbia                            | Europe                            |   26445000000.0 |              91884000.0 |        8202000.0 |                 3.6 |
| Seychelles                        | Africa                            |     773678000.0 |              98847000.0 |         156000.0 |                 5.2 |
| Sierra Leone                      | Africa                            |     495699000.0 |               2301000.0 |         149000.0 |                 9.1 |
| Singapore                         | East and Southeast Asia           |  365494000000.0 |             643259000.0 |       13134000.0 |                 2.8 |
| Slovakia                          | Europe                            |   11793000000.0 |             127582000.0 |        8138000.0 |                 4.6 |
| Slovenia                          | Europe                            |    2566000000.0 |             104502000.0 |        4739000.0 |                 5.8 |
| Solomon Islands                   | Australia and Oceania             |     688220000.0 |               5655000.0 |          37000.0 |                12.8 |
| Somalia                           | Africa                            |      30450000.0 |                649000.0 |         156000.0 |                 0.3 |
| South Africa                      | Africa                            |   54912000000.0 |              86197000.0 |       65989000.0 |                 6.6 |
| South Sudan                       | Africa                            |      72881000.0 |               2092000.0 |         136000.0 |                 1.5 |
| Spain                             | Europe                            |   84154000000.0 |             101120000.0 |      130366000.0 |                 4.6 |
| Sri Lanka                         | South Asia                        |    6055000000.0 |              12372000.0 |        5326000.0 |                 1.9 |
| Suriname                          | South America                     |    1530000000.0 |              60896000.0 |         537000.0 |                 5.0 |
| Sweden                            | Europe                            |   52023000000.0 |             142102000.0 |       55307000.0 |                 7.2 |
| Switzerland                       | Europe                            |  822130000000.0 |              99578000.0 |       26502000.0 |                 5.2 |
| Tajikistan                        | Central Asia                      |    3304000000.0 |              16192000.0 |        6481000.0 |                 5.9 |
| Tanzania                          | Africa                            |    5050000000.0 |               4091000.0 |        1818000.0 |                 3.3 |
| Thailand                          | East and Southeast Asia           |  217261000000.0 |              80602000.0 |       55971000.0 |                 3.2 |
| Timor-Leste                       | East and Southeast Asia           |     781995000.0 |               6825000.0 |         277000.0 |                 4.2 |
| Togo                              | Africa                            |      77800000.0 |               4538000.0 |         326000.0 |                 4.0 |
| Tonga                             | Australia and Oceania             |     396530000.0 |              23272000.0 |          34000.0 |                 6.6 |
| Tunisia                           | Africa                            |    8770000000.0 |              33754000.0 |        6639000.0 |                 7.3 |
| Turkey (Turkiye)                  | Middle East                       |   90859000000.0 |              70521000.0 |      106281000.0 |                 3.4 |
| Turkmenistan                      | Central Asia                      |   24910000000.0 |             261142000.0 |        6512000.0 |                 3.1 |
| Uganda                            | Africa                            |    3359000000.0 |               2252000.0 |        1452000.0 |                 2.7 |
| Ukraine                           | Europe                            |   41485000000.0 |              57856000.0 |       60297000.0 |                 5.4 |
| United Arab Emirates              | Middle East                       |  231690000000.0 |             450432000.0 |       44462000.0 |                 3.9 |
| United Kingdom                    | Europe                            |  148569000000.0 |              94280000.0 |      114749000.0 |                 5.5 |
| United States                     | North America                     |  227760000000.0 |             278474000.0 |     1235000000.0 |                 6.1 |
| Uruguay                           | South America                     |   17369000000.0 |              45755000.0 |        5682000.0 |                 4.6 |
| Uzbekistan                        | Central Asia                      |    9145000000.0 |              55305000.0 |       17901000.0 |                 4.9 |
| Vanuatu                           | Australia and Oceania             |     643768000.0 |              12934000.0 |          39000.0 |                 2.2 |
| Venezuela                         | South America                     |    9794000000.0 |              54474000.0 |       33493000.0 |                 1.3 |
| Vietnam                           | East and Southeast Asia           |   92238000000.0 |              40263000.0 |       85725000.0 |                 4.1 |
| West Bank                         | Middle East                       |    1328000000.0 |              14991000.0 |         352000.0 |                 5.3 |
| Zambia                            | Africa                            |    3173000000.0 |               8265000.0 |        3986000.0 |                 3.7 |
| Zimbabwe                          | Africa                            |     261373000.0 |              10855000.0 |        2491000.0 |                 3.9 |
+-----------------------------------+-----------------------------------+-----------------+-------------------------+------------------+---------------------+

 Datos guardados en 'dataset_final_consolidado.csv'
# Calcular estadísticos por región
stats = final_df.groupby('region').agg({
    'reserves_usd': ['mean', 'min', 'max'],
    'energy_btu_per_person': ['mean', 'min', 'max'],
    'electricity_kw': ['mean', 'min', 'max'],
    'education_pct_gdp': ['mean', 'min', 'max']
}).round(2)
# Renombrar columnas para mejor legibilidad
stats.columns = ['_'.join(col).strip() for col in stats.columns.values]
stats = stats.reset_index()
# Mostrar estadísticos con formato de tabla
print("\n ESTADÍSTICOS POR REGIÓN:")
print(tabulate(
    stats,
    headers='keys',
    tablefmt='psql',
    showindex=False,
    floatfmt=".2f"
))

# Guardar estadísticos a CSV
stats.to_csv('estadisticos_por_region.csv', index=False)
print("\n Estadísticos guardados en 'estadisticos_por_region.csv'")
 ESTADÍSTICOS POR REGIÓN:
+-----------------------------------+---------------------+--------------------+--------------------+------------------------------+-----------------------------+-----------------------------+-----------------------+----------------------+----------------------+--------------------------+-------------------------+-------------------------+
| region                            |   reserves_usd_mean |   reserves_usd_min |   reserves_usd_max |   energy_btu_per_person_mean |   energy_btu_per_person_min |   energy_btu_per_person_max |   electricity_kw_mean |   electricity_kw_min |   electricity_kw_max |   education_pct_gdp_mean |   education_pct_gdp_min |   education_pct_gdp_max |
|-----------------------------------+---------------------+--------------------+--------------------+------------------------------+-----------------------------+-----------------------------+-----------------------+----------------------+----------------------+--------------------------+-------------------------+-------------------------|
| Africa                            |       6860587300.00 |        30450000.00 |     68448000000.00 |                  14122840.00 |                   649000.00 |                 98847000.00 |            4619820.00 |             29000.00 |          65989000.00 |                     4.27 |                    0.30 |                    9.60 |
| Australia and Oceania             |      11479036857.14 |       396530000.00 |     54455000000.00 |                  62216714.29 |                  5655000.00 |                223158000.00 |           17061000.00 |             34000.00 |         108193000.00 |                     6.31 |                    2.20 |                   12.80 |
| Central America and the Caribbean |       4561869900.00 |       155971000.00 |     23834000000.00 |                  55873000.00 |                  3486000.00 |                153952000.00 |            1580500.00 |             41000.00 |           6581000.00 |                     4.56 |                    1.40 |                    8.70 |
| Central Asia                      |      75558222222.22 |      3237000000.00 |    597217000000.00 |                 103916111.11 |                 16192000.00 |                261142000.00 |           42395777.78 |           3944000.00 |         301926000.00 |                     4.33 |                    2.80 |                    6.20 |
| East and Southeast Asia           |     353614166388.89 |       781995000.00 |   3265000000000.00 |                 118687444.44 |                  6825000.00 |                643259000.00 |          210844555.56 |            277000.00 |        2949000000.00 |                     3.63 |                    1.40 |                    6.30 |
| Europe                            |      64158750542.86 |       921269000.00 |    822130000000.00 |                 113142228.57 |                 27407000.00 |                234698000.00 |           40275085.71 |            779000.00 |         275658000.00 |                     5.15 |                    3.10 |                    7.20 |
| Middle East                       |      94324785714.29 |      1328000000.00 |    436769000000.00 |                 240650857.14 |                 14991000.00 |                814308000.00 |           33817285.71 |            352000.00 |         119620000.00 |                     4.53 |                    1.70 |                    7.80 |
| North America                     |     189085000000.00 |    117551000000.00 |    227760000000.00 |                 215870666.67 |                 57539000.00 |                311599000.00 |          500858000.00 |         105586000.00 |        1235000000.00 |                     5.20 |                    4.30 |                    6.10 |
| South America                     |      47486925000.00 |        87100000.00 |    318857000000.00 |                  46414666.67 |                 25733000.00 |                 78496000.00 |           35504083.33 |            259000.00 |         240251000.00 |                     4.81 |                    1.30 |                    9.80 |
| South Asia                        |      79075929000.00 |       673203000.00 |    569544000000.00 |                  23506375.00 |                  3380000.00 |                 64082000.00 |           72116125.00 |            432000.00 |         499136000.00 |                     3.85 |                    1.90 |                    7.00 |
+-----------------------------------+---------------------+--------------------+--------------------+------------------------------+-----------------------------+-----------------------------+-----------------------+----------------------+----------------------+--------------------------+-------------------------+-------------------------+

 Estadísticos guardados en 'estadisticos_por_region.csv'
# Verificar qué columnas tenemos actualmente
print("Columnas existentes:", final_df.columns.tolist())

# Si las columnas de categorías no existen, las creamos
if 'reserves_usd_categoria' not in final_df.columns:
    # Definir función para crear categorías
    def crear_categorias(df, columna):
        try:
            return pd.qcut(
                df[columna],
                q=5,
                labels=['Muy bajo', 'Bajo', 'Medio', 'Alto', 'Muy alto'],
                duplicates='drop'
            )
        except:
            return pd.cut(
                df[columna],
                bins=5,
                labels=['Muy bajo', 'Bajo', 'Medio', 'Alto', 'Muy alto']
            )

    # Aplicar a cada variable
    final_df['reserves_usd_categoria'] = crear_categorias(final_df, 'reserves_usd')
    final_df['energy_btu_per_person_categoria'] = crear_categorias(final_df, 'energy_btu_per_person')
    final_df['electricity_kw_categoria'] = crear_categorias(final_df, 'electricity_kw')
    final_df['education_pct_gdp_categoria'] = crear_categorias(final_df, 'education_pct_gdp')
Columnas existentes: ['country', 'region', 'reserves_usd', 'energy_btu_per_person', 'electricity_kw', 'education_pct_gdp']
# Ahora reorganizar las columnas
column_order = ['country', 'region']
for var in ['reserves_usd', 'energy_btu_per_person', 'electricity_kw', 'education_pct_gdp']:
    column_order.extend([var, f'{var}_categoria'])
# Crear el DataFrame de visualización
final_df_display = final_df[column_order]
# Mostrar el dataset completo
print("\n DATASET COMPLETO CON CATEGORÍAS:")
print(tabulate(
    final_df_display,
    headers='keys',
    tablefmt='psql',
    showindex=False,
    floatfmt=".1f"
))

# Guardar el dataset
final_df_display.to_csv('dataset_final_con_categorias.csv', index=False)
print("\n Dataset guardado en 'dataset_final_con_categorias.csv'")
 DATASET COMPLETO CON CATEGORÍAS:
+-----------------------------------+-----------------------------------+-----------------+--------------------------+-------------------------+-----------------------------------+------------------+----------------------------+---------------------+-------------------------------+
| country                           | region                            |    reserves_usd | reserves_usd_categoria   |   energy_btu_per_person | energy_btu_per_person_categoria   |   electricity_kw | electricity_kw_categoria   |   education_pct_gdp | education_pct_gdp_categoria   |
|-----------------------------------+-----------------------------------+-----------------+--------------------------+-------------------------+-----------------------------------+------------------+----------------------------+---------------------+-------------------------------|
| Afghanistan                       | South Asia                        |    9749000000.0 | Medio                    |               3380000.0 | Muy bajo                          |         627000.0 | Bajo                       |                 2.9 | Muy bajo                      |
| Albania                           | Europe                            |    6455000000.0 | Medio                    |              27407000.0 | Bajo                              |        2857000.0 | Medio                      |                 3.1 | Muy bajo                      |
| Algeria                           | Africa                            |   68448000000.0 | Muy alto                 |              61843000.0 | Medio                             |       22591000.0 | Alto                       |                 7.0 | Muy alto                      |
| Angola                            | Africa                            |   14243000000.0 | Alto                     |               9146000.0 | Bajo                              |        7600000.0 | Alto                       |                 2.4 | Muy bajo                      |
| Antigua and Barbuda               | Central America and the Caribbean |     358441000.0 | Muy bajo                 |             110114000.0 | Alto                              |         148000.0 | Muy bajo                   |                 3.8 | Bajo                          |
| Argentina                         | South America                     |   24381000000.0 | Alto                     |              78496000.0 | Alto                              |       47631000.0 | Muy alto                   |                 5.0 | Alto                          |
| Armenia                           | Central Asia                      |    3684000000.0 | Bajo                     |              54689000.0 | Medio                             |        4265000.0 | Medio                      |                 2.8 | Muy bajo                      |
| Aruba                             | Central America and the Caribbean |    1468000000.0 | Bajo                     |             153952000.0 | Muy alto                          |         305000.0 | Muy bajo                   |                 5.5 | Alto                          |
| Australia                         | Australia and Oceania             |   54455000000.0 | Alto                     |             223158000.0 | Muy alto                          |      108193000.0 | Muy alto                   |                 6.1 | Muy alto                      |
| Austria                           | Europe                            |   11919000000.0 | Alto                     |             120211000.0 | Muy alto                          |       33371000.0 | Muy alto                   |                 5.1 | Alto                          |
| Azerbaijan                        | Central Asia                      |   12699000000.0 | Alto                     |              66467000.0 | Alto                              |        8383000.0 | Alto                       |                 4.3 | Medio                         |
| Bahamas, The                      | Central America and the Caribbean |    2512000000.0 | Bajo                     |             104409000.0 | Alto                              |         608000.0 | Bajo                       |                 2.8 | Muy bajo                      |
| Bahrain                           | Middle East                       |    5118000000.0 | Medio                    |             554202000.0 | Muy alto                          |        7031000.0 | Medio                      |                 2.2 | Muy bajo                      |
| Bangladesh                        | South Asia                        |   20225000000.0 | Alto                     |              11472000.0 | Bajo                              |       22699000.0 | Alto                       |                 2.1 | Muy bajo                      |
| Barbados                          | Central America and the Caribbean |    1606000000.0 | Bajo                     |              68293000.0 | Alto                              |         320000.0 | Muy bajo                   |                 6.5 | Muy alto                      |
| Belarus                           | Europe                            |    4392000000.0 | Medio                    |             104821000.0 | Alto                              |       12653000.0 | Alto                       |                 4.7 | Medio                         |
| Belgium                           | Europe                            |   22374000000.0 | Alto                     |             190416000.0 | Muy alto                          |       28248000.0 | Alto                       |                 6.7 | Muy alto                      |
| Belize                            | Central America and the Caribbean |     498087000.0 | Muy bajo                 |              30752000.0 | Medio                             |         220000.0 | Muy bajo                   |                 8.7 | Muy alto                      |
| Benin                             | Africa                            |     698900000.0 | Muy bajo                 |               6472000.0 | Muy bajo                          |         505000.0 | Bajo                       |                 3.0 | Muy bajo                      |
| Bhutan                            | South Asia                        |     928229000.0 | Bajo                     |              64082000.0 | Medio                             |        2344000.0 | Bajo                       |                 7.0 | Muy alto                      |
| Bolivia                           | South America                     |      87100000.0 | Muy bajo                 |              29340000.0 | Medio                             |        4375000.0 | Medio                      |                 9.8 | Muy alto                      |
| Botswana                          | Africa                            |    4756000000.0 | Medio                    |              32443000.0 | Medio                             |         758000.0 | Bajo                       |                 8.7 | Muy alto                      |
| Brazil                            | South America                     |  318857000000.0 | Muy alto                 |              48889000.0 | Medio                             |      240251000.0 | Muy alto                   |                 6.0 | Muy alto                      |
| Brunei                            | East and Southeast Asia           |    4032000000.0 | Medio                    |             403365000.0 | Muy alto                          |         904000.0 | Bajo                       |                 4.4 | Medio                         |
| Bulgaria                          | Europe                            |   40270000000.0 | Alto                     |             102171000.0 | Alto                              |       12939000.0 | Alto                       |                 4.0 | Medio                         |
| Burkina Faso                      | Africa                            |      49000000.0 | Muy bajo                 |               3481000.0 | Muy bajo                          |         749000.0 | Bajo                       |                 5.5 | Alto                          |
| Burma                             | East and Southeast Asia           |    9338000000.0 | Medio                    |               8384000.0 | Bajo                              |        7419000.0 | Medio                      |                 2.1 | Muy bajo                      |
| Burundi                           | Africa                            |      90350000.0 | Muy bajo                 |                946000.0 | Muy bajo                          |         131000.0 | Muy bajo                   |                 5.0 | Alto                          |
| Cabo Verde                        | Africa                            |     783106000.0 | Muy bajo                 |              19999000.0 | Bajo                              |         200000.0 | Muy bajo                   |                 7.6 | Muy alto                      |
| Cambodia                          | East and Southeast Asia           |   18608000000.0 | Alto                     |              15664000.0 | Bajo                              |        3673000.0 | Medio                      |                 3.1 | Muy bajo                      |
| Cameroon                          | Africa                            |    4882000000.0 | Medio                    |               4271000.0 | Muy bajo                          |        1798000.0 | Bajo                       |                 3.2 | Bajo                          |
| Canada                            | North America                     |  117551000000.0 | Muy alto                 |             311599000.0 | Muy alto                          |      161988000.0 | Muy alto                   |                 5.2 | Alto                          |
| Cayman Islands                    | Central America and the Caribbean |     234000000.0 | Muy bajo                 |             143149000.0 | Muy alto                          |         176000.0 | Muy bajo                   |                 2.1 | Muy bajo                      |
| Central African Republic          | Africa                            |     479593000.0 | Muy bajo                 |                954000.0 | Muy bajo                          |          63000.0 | Muy bajo                   |                 2.2 | Muy bajo                      |
| Chad                              | Africa                            |    1050000000.0 | Bajo                     |               1502000.0 | Muy bajo                          |         167000.0 | Muy bajo                   |                 2.9 | Muy bajo                      |
| Chile                             | South America                     |   44382000000.0 | Alto                     |              71420000.0 | Alto                              |       39238000.0 | Muy alto                   |                 5.6 | Alto                          |
| China                             | East and Southeast Asia           | 3265000000000.0 | Muy alto                 |             113805000.0 | Muy alto                          |     2949000000.0 | Muy alto                   |                 3.6 | Bajo                          |
| Colombia                          | South America                     |   61506000000.0 | Alto                     |              29305000.0 | Bajo                              |       21053000.0 | Alto                       |                 4.9 | Medio                         |
| Comoros                           | Africa                            |     322436000.0 | Muy bajo                 |               7139000.0 | Muy bajo                          |          32000.0 | Muy bajo                   |                 2.6 | Muy bajo                      |
| Congo, Democratic Republic of the | Africa                            |    5104000000.0 | Medio                    |               1305000.0 | Muy bajo                          |        3229000.0 | Medio                      |                 2.7 | Muy bajo                      |
| Congo, Republic of the            | Africa                            |     715391000.0 | Muy bajo                 |               7351000.0 | Muy bajo                          |         842000.0 | Bajo                       |                 4.5 | Medio                         |
| Costa Rica                        | Central America and the Caribbean |   14177000000.0 | Alto                     |              30725000.0 | Medio                             |        3751000.0 | Medio                      |                 6.7 | Muy alto                      |
| Cote d'Ivoire                     | Africa                            |    6257000000.0 | Medio                    |               8489000.0 | Bajo                              |        2315000.0 | Bajo                       |                 3.4 | Bajo                          |
| Croatia                           | Europe                            |    3336000000.0 | Bajo                     |              79907000.0 | Alto                              |        5518000.0 | Medio                      |                 5.5 | Alto                          |
| Cyprus                            | Europe                            |     921269000.0 | Bajo                     |             107188000.0 | Alto                              |        2288000.0 | Bajo                       |                 6.1 | Muy alto                      |
| Czechia                           | Europe                            |  141988000000.0 | Muy alto                 |             136306000.0 | Muy alto                          |       21802000.0 | Alto                       |                 5.1 | Alto                          |
| Denmark                           | Europe                            |  103239000000.0 | Muy alto                 |              98513000.0 | Alto                              |       20794000.0 | Alto                       |                 6.4 | Muy alto                      |
| Djibouti                          | Africa                            |     348725000.0 | Muy bajo                 |              10428000.0 | Bajo                              |         210000.0 | Muy bajo                   |                 3.6 | Bajo                          |
| Dominica                          | Central America and the Caribbean |     155971000.0 | Muy bajo                 |              36395000.0 | Medio                             |          41000.0 | Muy bajo                   |                 5.5 | Alto                          |
| Dominican Republic                | Central America and the Caribbean |   13424000000.0 | Alto                     |              39329000.0 | Medio                             |        6581000.0 | Medio                      |                 4.6 | Medio                         |
| Ecuador                           | South America                     |    4703000000.0 | Medio                    |              35700000.0 | Medio                             |        8438000.0 | Alto                       |                 3.7 | Bajo                          |
| Egypt                             | Africa                            |   34278000000.0 | Alto                     |              34975000.0 | Medio                             |       59680000.0 | Muy alto                   |                 2.5 | Muy bajo                      |
| El Salvador                       | Central America and the Caribbean |    3590000000.0 | Bajo                     |              24421000.0 | Bajo                              |        2803000.0 | Medio                      |                 4.1 | Medio                         |
| Estonia                           | Europe                            |    2054000000.0 | Bajo                     |              73679000.0 | Alto                              |        3225000.0 | Medio                      |                 6.6 | Muy alto                      |
| Eswatini                          | Africa                            |     479261000.0 | Muy bajo                 |              18823000.0 | Bajo                              |         285000.0 | Muy bajo                   |                 5.0 | Alto                          |
| Ethiopia                          | Africa                            |    3784000000.0 | Medio                    |               2366000.0 | Muy bajo                          |        5690000.0 | Medio                      |                 4.5 | Medio                         |
| Fiji                              | Australia and Oceania             |    1597000000.0 | Bajo                     |              25375000.0 | Bajo                              |         427000.0 | Bajo                       |                 5.7 | Alto                          |
| Finland                           | Europe                            |   14322000000.0 | Alto                     |             183540000.0 | Muy alto                          |       26782000.0 | Alto                       |                 5.9 | Alto                          |
| France                            | Europe                            |   78429000000.0 | Muy alto                 |             123526000.0 | Muy alto                          |      151463000.0 | Muy alto                   |                 5.5 | Alto                          |
| Gabon                             | Africa                            |    1447000000.0 | Bajo                     |              22101000.0 | Bajo                              |         785000.0 | Bajo                       |                 3.2 | Bajo                          |
| Gambia, The                       | Africa                            |     577028000.0 | Muy bajo                 |               2731000.0 | Muy bajo                          |         162000.0 | Muy bajo                   |                 2.8 | Muy bajo                      |
| Gaza Strip                        | Middle East                       |    1328000000.0 | Bajo                     |              14991000.0 | Bajo                              |         352000.0 | Muy bajo                   |                 5.3 | Alto                          |
| Georgia                           | Central Asia                      |    3848000000.0 | Medio                    |              56076000.0 | Medio                             |        4526000.0 | Medio                      |                 3.6 | Bajo                          |
| Germany                           | Europe                            |   96792000000.0 | Muy alto                 |             120457000.0 | Muy alto                          |      275658000.0 | Muy alto                   |                 4.7 | Medio                         |
| Ghana                             | Africa                            |    3624000000.0 | Bajo                     |              10493000.0 | Bajo                              |        5519000.0 | Medio                      |                 3.9 | Bajo                          |
| Greece                            | Europe                            |    5610000000.0 | Medio                    |              92693000.0 | Alto                              |       24169000.0 | Alto                       |                 4.4 | Medio                         |
| Grenada                           | Central America and the Caribbean |     423263000.0 | Muy bajo                 |              41703000.0 | Medio                             |          60000.0 | Muy bajo                   |                 3.6 | Bajo                          |
| Guatemala                         | Central America and the Caribbean |   23834000000.0 | Alto                     |              17096000.0 | Bajo                              |        4995000.0 | Medio                      |                 3.1 | Muy bajo                      |
| Guinea                            | Africa                            |    1887000000.0 | Bajo                     |               5235000.0 | Muy bajo                          |        1060000.0 | Bajo                       |                 2.2 | Muy bajo                      |
| Guinea-Bissau                     | Africa                            |     356400000.0 | Muy bajo                 |               2351000.0 | Muy bajo                          |          29000.0 | Muy bajo                   |                 2.7 | Muy bajo                      |
| Guyana                            | South America                     |    1010000000.0 | Bajo                     |              46045000.0 | Medio                             |         259000.0 | Muy bajo                   |                 4.5 | Medio                         |
| Haiti                             | Central America and the Caribbean |    2566000000.0 | Bajo                     |               3486000.0 | Muy bajo                          |         472000.0 | Bajo                       |                 1.4 | Muy bajo                      |
| Honduras                          | Central America and the Caribbean |    7543000000.0 | Medio                    |              16642000.0 | Bajo                              |        3334000.0 | Medio                      |                 6.4 | Muy alto                      |
| Hong Kong                         | East and Southeast Asia           |  425554000000.0 | Muy alto                 |             116811000.0 | Muy alto                          |       13300000.0 | Alto                       |                 4.0 | Medio                         |
| Hungary                           | Europe                            |   37194000000.0 | Alto                     |              96152000.0 | Alto                              |       14829000.0 | Alto                       |                 4.8 | Medio                         |
| India                             | South Asia                        |  569544000000.0 | Muy alto                 |              25179000.0 | Bajo                              |      499136000.0 | Muy alto                   |                 4.5 | Medio                         |
| Indonesia                         | East and Southeast Asia           |  149118000000.0 | Muy alto                 |              37390000.0 | Medio                             |       70826000.0 | Muy alto                   |                 3.5 | Bajo                          |
| Iran                              | Middle East                       |  120600000000.0 | Muy alto                 |             160779000.0 | Muy alto                          |       86058000.0 | Muy alto                   |                 3.6 | Bajo                          |
| Iraq                              | Middle East                       |   87048000000.0 | Muy alto                 |              64311000.0 | Alto                              |       31339000.0 | Alto                       |                 4.7 | Medio                         |
| Ireland                           | Europe                            |   11688000000.0 | Medio                    |             113837000.0 | Muy alto                          |       12321000.0 | Alto                       |                 3.1 | Muy bajo                      |
| Israel                            | Middle East                       |  214544000000.0 | Muy alto                 |             112437000.0 | Alto                              |       22612000.0 | Alto                       |                 7.1 | Muy alto                      |
| Italy                             | Europe                            |   84875000000.0 | Muy alto                 |              96797000.0 | Alto                              |      128692000.0 | Muy alto                   |                 4.3 | Medio                         |
| Jamaica                           | Central America and the Caribbean |    4869000000.0 | Medio                    |              42095000.0 | Medio                             |        1242000.0 | Bajo                       |                 6.0 | Muy alto                      |
| Japan                             | East and Southeast Asia           | 1160000000000.0 | Muy alto                 |             129504000.0 | Muy alto                          |      361617000.0 | Muy alto                   |                 3.4 | Bajo                          |
| Jordan                            | Middle East                       |   15929000000.0 | Alto                     |              32909000.0 | Medio                             |        6891000.0 | Medio                      |                 3.2 | Bajo                          |
| Kazakhstan                        | Central Asia                      |   21980000000.0 | Alto                     |             172936000.0 | Muy alto                          |       27624000.0 | Alto                       |                 4.5 | Medio                         |
| Kenya                             | Africa                            |   10065000000.0 | Medio                    |               5486000.0 | Muy bajo                          |        3824000.0 | Medio                      |                 4.8 | Medio                         |
| Korea, South                      | East and Southeast Asia           |  409457000000.0 | Muy alto                 |             234668000.0 | Muy alto                          |      151139000.0 | Muy alto                   |                 4.7 | Medio                         |
| Kuwait                            | Middle East                       |   44104000000.0 | Alto                     |             389848000.0 | Muy alto                          |       20294000.0 | Alto                       |                 6.6 | Muy alto                      |
| Kyrgyzstan                        | Central Asia                      |    3237000000.0 | Bajo                     |              27580000.0 | Bajo                              |        3944000.0 | Medio                      |                 6.2 | Muy alto                      |
| Laos                              | East and Southeast Asia           |    1770000000.0 | Bajo                     |              34463000.0 | Medio                             |       12738000.0 | Alto                       |                 2.3 | Muy bajo                      |
| Latvia                            | Europe                            |    4591000000.0 | Medio                    |              65908000.0 | Alto                              |        3428000.0 | Medio                      |                 6.0 | Muy alto                      |
| Lebanon                           | Middle East                       |    9240000000.0 | Medio                    |              43105000.0 | Medio                             |        5161000.0 | Medio                      |                 1.7 | Muy bajo                      |
| Lesotho                           | Africa                            |     854089000.0 | Bajo                     |               8117000.0 | Muy bajo                          |         104000.0 | Muy bajo                   |                 8.7 | Muy alto                      |
| Liberia                           | Africa                            |     599660000.0 | Muy bajo                 |               1822000.0 | Muy bajo                          |         199000.0 | Muy bajo                   |                 2.7 | Muy bajo                      |
| Lithuania                         | Europe                            |    6920000000.0 | Medio                    |              83700000.0 | Alto                              |        5426000.0 | Medio                      |                 4.0 | Medio                         |
| Luxembourg                        | Europe                            |    2602000000.0 | Bajo                     |             224651000.0 | Muy alto                          |        2212000.0 | Bajo                       |                 5.0 | Alto                          |
| Macau                             | East and Southeast Asia           |   29392000000.0 | Alto                     |              64641000.0 | Alto                              |         427000.0 | Bajo                       |                 6.3 | Muy alto                      |
| Madagascar                        | Africa                            |    2785000000.0 | Bajo                     |               1816000.0 | Muy bajo                          |         759000.0 | Bajo                       |                 3.1 | Muy bajo                      |
| Malawi                            | Africa                            |     594498000.0 | Muy bajo                 |               1792000.0 | Muy bajo                          |         731000.0 | Bajo                       |                 2.9 | Muy bajo                      |
| Malaysia                          | East and Southeast Asia           |  112968000000.0 | Muy alto                 |             113163000.0 | Alto                              |       37220000.0 | Muy alto                   |                 3.9 | Bajo                          |
| Maldives                          | South Asia                        |     673203000.0 | Muy bajo                 |              50886000.0 | Medio                             |         432000.0 | Bajo                       |                 5.8 | Alto                          |
| Mali                              | Africa                            |     647800000.0 | Muy bajo                 |               4307000.0 | Muy bajo                          |        1222000.0 | Bajo                       |                 4.4 | Medio                         |
| Malta                             | Europe                            |    1402000000.0 | Bajo                     |             234698000.0 | Muy alto                          |         829000.0 | Bajo                       |                 5.9 | Alto                          |
| Mauritania                        | Africa                            |    2039000000.0 | Bajo                     |              14135000.0 | Bajo                              |         812000.0 | Bajo                       |                 1.9 | Muy bajo                      |
| Mauritius                         | Africa                            |    7465000000.0 | Medio                    |              60188000.0 | Medio                             |         955000.0 | Bajo                       |                 4.9 | Medio                         |
| Mexico                            | North America                     |  221944000000.0 | Muy alto                 |              57539000.0 | Medio                             |      105586000.0 | Muy alto                   |                 4.3 | Medio                         |
| Moldova                           | Europe                            |    5477000000.0 | Medio                    |              35686000.0 | Medio                             |         779000.0 | Bajo                       |                 6.4 | Muy alto                      |
| Mongolia                          | East and Southeast Asia           |    4891000000.0 | Medio                    |              67132000.0 | Alto                              |        1510000.0 | Bajo                       |                 4.7 | Medio                         |
| Morocco                           | Africa                            |   35269000000.0 | Alto                     |              23520000.0 | Bajo                              |       14615000.0 | Alto                       |                 6.8 | Muy alto                      |
| Mozambique                        | Africa                            |    3513000000.0 | Bajo                     |               5789000.0 | Muy bajo                          |        2860000.0 | Medio                      |                 6.3 | Muy alto                      |
| Namibia                           | Africa                            |    3356000000.0 | Bajo                     |              21734000.0 | Bajo                              |         646000.0 | Bajo                       |                 9.6 | Muy alto                      |
| Nepal                             | South Asia                        |   12456000000.0 | Alto                     |               6604000.0 | Muy bajo                          |        2853000.0 | Medio                      |                 4.2 | Medio                         |
| Netherlands                       | Europe                            |   27753000000.0 | Alto                     |             185536000.0 | Muy alto                          |       59982000.0 | Muy alto                   |                 5.3 | Alto                          |
| New Zealand                       | Australia and Oceania             |   22065000000.0 | Alto                     |             121647000.0 | Muy alto                          |       10643000.0 | Alto                       |                 6.0 | Muy alto                      |
| Nicaragua                         | Central America and the Caribbean |    6105000000.0 | Medio                    |              10660000.0 | Bajo                              |        1849000.0 | Bajo                       |                 4.6 | Medio                         |
| Niger                             | Africa                            |    1314000000.0 | Bajo                     |               1772000.0 | Muy bajo                          |         377000.0 | Bajo                       |                 3.8 | Bajo                          |
| Nigeria                           | Africa                            |   38612000000.0 | Alto                     |               7993000.0 | Muy bajo                          |        4094000.0 | Medio                      |                 0.5 | Muy bajo                      |
| Norway                            | Europe                            |   81242000000.0 | Muy alto                 |             206961000.0 | Muy alto                          |       41100000.0 | Muy alto                   |                 5.9 | Alto                          |
| Oman                              | Middle East                       |   17298000000.0 | Alto                     |             296586000.0 | Muy alto                          |       11589000.0 | Alto                       |                 5.4 | Alto                          |
| Pakistan                          | South Asia                        |   12977000000.0 | Alto                     |              14076000.0 | Bajo                              |       43512000.0 | Muy alto                   |                 2.4 | Muy bajo                      |
| Panama                            | Central America and the Caribbean |    6856000000.0 | Medio                    |              78010000.0 | Alto                              |        4485000.0 | Medio                      |                 3.9 | Bajo                          |
| Papua New Guinea                  | East and Southeast Asia           |    3901000000.0 | Medio                    |               8781000.0 | Bajo                              |        1148000.0 | Bajo                       |                 1.4 | Muy bajo                      |
| Paraguay                          | South America                     |    9886000000.0 | Medio                    |              25733000.0 | Bajo                              |        8928000.0 | Alto                       |                 3.3 | Bajo                          |
| Peru                              | South America                     |   76338000000.0 | Muy alto                 |              30923000.0 | Medio                             |       16164000.0 | Alto                       |                 4.0 | Medio                         |
| Philippines                       | East and Southeast Asia           |   95251000000.0 | Muy alto                 |              17654000.0 | Bajo                              |       29174000.0 | Alto                       |                 3.7 | Bajo                          |
| Poland                            | Europe                            |  185515000000.0 | Muy alto                 |             103651000.0 | Alto                              |       64806000.0 | Muy alto                   |                 5.2 | Alto                          |
| Portugal                          | Europe                            |   10332000000.0 | Medio                    |              73285000.0 | Alto                              |       25409000.0 | Alto                       |                 5.0 | Alto                          |
| Qatar                             | Middle East                       |   44692000000.0 | Alto                     |             814308000.0 | Muy alto                          |       11400000.0 | Alto                       |                 3.2 | Bajo                          |
| Romania                           | Europe                            |   64699000000.0 | Muy alto                 |              59377000.0 | Medio                             |       19748000.0 | Alto                       |                 3.7 | Bajo                          |
| Russia                            | Central Asia                      |  597217000000.0 | Muy alto                 |             224858000.0 | Muy alto                          |      301926000.0 | Muy alto                   |                 3.7 | Bajo                          |
| Rwanda                            | Africa                            |    2406000000.0 | Bajo                     |               1808000.0 | Muy bajo                          |         294000.0 | Muy bajo                   |                 3.8 | Bajo                          |
| Saint Kitts and Nevis             | Central America and the Caribbean |     294748000.0 | Muy bajo                 |              81454000.0 | Alto                              |          72000.0 | Muy bajo                   |                 2.5 | Muy bajo                      |
| Saint Lucia                       | Central America and the Caribbean |     406064000.0 | Muy bajo                 |              47522000.0 | Medio                             |          93000.0 | Muy bajo                   |                 3.6 | Bajo                          |
| Saint Vincent and the Grenadines  | Central America and the Caribbean |     316824000.0 | Muy bajo                 |              37253000.0 | Medio                             |          55000.0 | Muy bajo                   |                 5.7 | Alto                          |
| Samoa                             | Australia and Oceania             |     507740000.0 | Muy bajo                 |              23476000.0 | Bajo                              |          54000.0 | Muy bajo                   |                 4.8 | Medio                         |
| Sao Tome and Principe             | Africa                            |      46247000.0 | Muy bajo                 |               8875000.0 | Bajo                              |          29000.0 | Muy bajo                   |                 5.0 | Alto                          |
| Saudi Arabia                      | Middle East                       |  436769000000.0 | Muy alto                 |             349692000.0 | Muy alto                          |      119620000.0 | Muy alto                   |                 7.8 | Muy alto                      |
| Senegal                           | Africa                            |    1827000000.0 | Bajo                     |               8303000.0 | Muy bajo                          |        1772000.0 | Bajo                       |                 5.5 | Alto                          |
| Serbia                            | Europe                            |   26445000000.0 | Alto                     |              91884000.0 | Alto                              |        8202000.0 | Alto                       |                 3.6 | Bajo                          |
| Seychelles                        | Africa                            |     773678000.0 | Muy bajo                 |              98847000.0 | Alto                              |         156000.0 | Muy bajo                   |                 5.2 | Alto                          |
| Sierra Leone                      | Africa                            |     495699000.0 | Muy bajo                 |               2301000.0 | Muy bajo                          |         149000.0 | Muy bajo                   |                 9.1 | Muy alto                      |
| Singapore                         | East and Southeast Asia           |  365494000000.0 | Muy alto                 |             643259000.0 | Muy alto                          |       13134000.0 | Alto                       |                 2.8 | Muy bajo                      |
| Slovakia                          | Europe                            |   11793000000.0 | Medio                    |             127582000.0 | Muy alto                          |        8138000.0 | Alto                       |                 4.6 | Medio                         |
| Slovenia                          | Europe                            |    2566000000.0 | Bajo                     |             104502000.0 | Alto                              |        4739000.0 | Medio                      |                 5.8 | Alto                          |
| Solomon Islands                   | Australia and Oceania             |     688220000.0 | Muy bajo                 |               5655000.0 | Muy bajo                          |          37000.0 | Muy bajo                   |                12.8 | Muy alto                      |
| Somalia                           | Africa                            |      30450000.0 | Muy bajo                 |                649000.0 | Muy bajo                          |         156000.0 | Muy bajo                   |                 0.3 | Muy bajo                      |
| South Africa                      | Africa                            |   54912000000.0 | Alto                     |              86197000.0 | Alto                              |       65989000.0 | Muy alto                   |                 6.6 | Muy alto                      |
| South Sudan                       | Africa                            |      72881000.0 | Muy bajo                 |               2092000.0 | Muy bajo                          |         136000.0 | Muy bajo                   |                 1.5 | Muy bajo                      |
| Spain                             | Europe                            |   84154000000.0 | Muy alto                 |             101120000.0 | Alto                              |      130366000.0 | Muy alto                   |                 4.6 | Medio                         |
| Sri Lanka                         | South Asia                        |    6055000000.0 | Medio                    |              12372000.0 | Bajo                              |        5326000.0 | Medio                      |                 1.9 | Muy bajo                      |
| Suriname                          | South America                     |    1530000000.0 | Bajo                     |              60896000.0 | Medio                             |         537000.0 | Bajo                       |                 5.0 | Alto                          |
| Sweden                            | Europe                            |   52023000000.0 | Alto                     |             142102000.0 | Muy alto                          |       55307000.0 | Muy alto                   |                 7.2 | Muy alto                      |
| Switzerland                       | Europe                            |  822130000000.0 | Muy alto                 |              99578000.0 | Alto                              |       26502000.0 | Alto                       |                 5.2 | Alto                          |
| Tajikistan                        | Central Asia                      |    3304000000.0 | Bajo                     |              16192000.0 | Bajo                              |        6481000.0 | Medio                      |                 5.9 | Alto                          |
| Tanzania                          | Africa                            |    5050000000.0 | Medio                    |               4091000.0 | Muy bajo                          |        1818000.0 | Bajo                       |                 3.3 | Bajo                          |
| Thailand                          | East and Southeast Asia           |  217261000000.0 | Muy alto                 |              80602000.0 | Alto                              |       55971000.0 | Muy alto                   |                 3.2 | Bajo                          |
| Timor-Leste                       | East and Southeast Asia           |     781995000.0 | Muy bajo                 |               6825000.0 | Muy bajo                          |         277000.0 | Muy bajo                   |                 4.2 | Medio                         |
| Togo                              | Africa                            |      77800000.0 | Muy bajo                 |               4538000.0 | Muy bajo                          |         326000.0 | Muy bajo                   |                 4.0 | Medio                         |
| Tonga                             | Australia and Oceania             |     396530000.0 | Muy bajo                 |              23272000.0 | Bajo                              |          34000.0 | Muy bajo                   |                 6.6 | Muy alto                      |
| Tunisia                           | Africa                            |    8770000000.0 | Medio                    |              33754000.0 | Medio                             |        6639000.0 | Medio                      |                 7.3 | Muy alto                      |
| Turkey (Turkiye)                  | Middle East                       |   90859000000.0 | Muy alto                 |              70521000.0 | Alto                              |      106281000.0 | Muy alto                   |                 3.4 | Bajo                          |
| Turkmenistan                      | Central Asia                      |   24910000000.0 | Alto                     |             261142000.0 | Muy alto                          |        6512000.0 | Medio                      |                 3.1 | Muy bajo                      |
| Uganda                            | Africa                            |    3359000000.0 | Bajo                     |               2252000.0 | Muy bajo                          |        1452000.0 | Bajo                       |                 2.7 | Muy bajo                      |
| Ukraine                           | Europe                            |   41485000000.0 | Alto                     |              57856000.0 | Medio                             |       60297000.0 | Muy alto                   |                 5.4 | Alto                          |
| United Arab Emirates              | Middle East                       |  231690000000.0 | Muy alto                 |             450432000.0 | Muy alto                          |       44462000.0 | Muy alto                   |                 3.9 | Bajo                          |
| United Kingdom                    | Europe                            |  148569000000.0 | Muy alto                 |              94280000.0 | Alto                              |      114749000.0 | Muy alto                   |                 5.5 | Alto                          |
| United States                     | North America                     |  227760000000.0 | Muy alto                 |             278474000.0 | Muy alto                          |     1235000000.0 | Muy alto                   |                 6.1 | Muy alto                      |
| Uruguay                           | South America                     |   17369000000.0 | Alto                     |              45755000.0 | Medio                             |        5682000.0 | Medio                      |                 4.6 | Medio                         |
| Uzbekistan                        | Central Asia                      |    9145000000.0 | Medio                    |              55305000.0 | Medio                             |       17901000.0 | Alto                       |                 4.9 | Medio                         |
| Vanuatu                           | Australia and Oceania             |     643768000.0 | Muy bajo                 |              12934000.0 | Bajo                              |          39000.0 | Muy bajo                   |                 2.2 | Muy bajo                      |
| Venezuela                         | South America                     |    9794000000.0 | Medio                    |              54474000.0 | Medio                             |       33493000.0 | Muy alto                   |                 1.3 | Muy bajo                      |
| Vietnam                           | East and Southeast Asia           |   92238000000.0 | Muy alto                 |              40263000.0 | Medio                             |       85725000.0 | Muy alto                   |                 4.1 | Medio                         |
| West Bank                         | Middle East                       |    1328000000.0 | Bajo                     |              14991000.0 | Bajo                              |         352000.0 | Muy bajo                   |                 5.3 | Alto                          |
| Zambia                            | Africa                            |    3173000000.0 | Bajo                     |               8265000.0 | Muy bajo                          |        3986000.0 | Medio                      |                 3.7 | Bajo                          |
| Zimbabwe                          | Africa                            |     261373000.0 | Muy bajo                 |              10855000.0 | Bajo                              |        2491000.0 | Medio                      |                 3.9 | Bajo                          |
+-----------------------------------+-----------------------------------+-----------------+--------------------------+-------------------------+-----------------------------------+------------------+----------------------------+---------------------+-------------------------------+

 Dataset guardado en 'dataset_final_con_categorias.csv'
import pandas as pd
```
