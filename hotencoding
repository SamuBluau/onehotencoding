import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Título de la aplicación
st.title("Visualización de Reglas de Lipinski")
st.write("Sube un archivo Excel con datos de moléculas para analizar los donantes y aceptores de enlaces de hidrógeno.")

# Subida de archivos Excel
uploaded_file = st.file_uploader("Sube un archivo Excel", type=["xlsx"])

if uploaded_file:
    try:
        # Leer el archivo Excel
        df = pd.read_excel(uploaded_file)

        # Verificar si las columnas esperadas existen
        expected_columns = ["Donantes_H", "Aceptores_H"]
        if not all(col in df.columns for col in expected_columns):
            st.error("El archivo debe contener las columnas 'Donantes_H' y 'Aceptores_H'.")
        else:
            # Filtrar valores en el rango esperado
            df_filtered = df[(df["Donantes_H"] >= 0) & (df["Donantes_H"] <= 5) & 
                             (df["Aceptores_H"] >= 0) & (df["Aceptores_H"] <= 10)]
            
            # Aplicar One-Hot Encoding para verificar si cumple con las reglas de Lipinski
            df_filtered["Cumple_Regla"] = ((df_filtered["Donantes_H"] < 5) & (df_filtered["Aceptores_H"] < 10)).astype(int)
            
            # Calcular el área de la matriz proporcionada por One-Hot Encoding
            area_matriz = df_filtered["Cumple_Regla"].sum()
            
            # Mostrar el área calculada
            st.write(f"Área de la matriz (cantidad de moléculas que cumplen las reglas): {area_matriz}")
            
            # Crear un gráfico de dispersión
            fig, ax = plt.subplots()
            sns.scatterplot(data=df_filtered, x="Aceptores_H", y="Donantes_H", hue="Cumple_Regla", palette={0: "red", 1: "green"}, ax=ax)
            ax.set_xlim(0, 10)
            ax.set_ylim(0, 5)
            ax.set_title("Distribución de Donantes y Aceptores de Hidrógeno")
            st.pyplot(fig)
            
            # Mostrar tabla con los datos procesados
            st.write("### Datos Procesados")
            st.dataframe(df_filtered)
            
            # Botón para descargar los datos procesados
            @st.cache_data
            def convert_df_to_csv(df):
                return df.to_csv(index=False).encode("utf-8")
            
            csv = convert_df_to_csv(df_filtered)
            st.download_button(
                label="Descargar Datos Procesados",
                data=csv,
                file_name="datos_procesados.csv",
                mime="text/csv"
            )
    
    except Exception as e:
        st.error(f"Error al procesar el archivo: {e}")
