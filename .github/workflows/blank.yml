import streamlit as st
import requests
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Função para formatar preço em reais (se necessário)
def formatar_preco_reais(valor):
    if valor is None:
        return 'Preço não disponível'
    else:
        return f'{valor:,.2f}'.replace(',', 'X').replace('.', ',').replace('X', '.')

# URL fornecida
url = "http://compras.dados.gov.br/licitacoes/v1/orgaos.json?nome=turismo"

def obter_dados():
    try:
        response = requests.get(url)
        if response.status_code == 200:
            data = response.json()
            if '_embedded' in data and 'orgao' in data['_embedded']:
                return data['_embedded']['orgao']
            else:
                st.error("Nenhum dado 'orgao' encontrado na resposta.")
                st.json(data)  # Exibir a resposta para inspeção
                return []
        else:
            st.error(f"Erro na requisição: {response.status_code}")
            st.text(response.text)  # Exibir a resposta bruta para ver o que foi retornado
            return []
    except Exception as e:
        st.error(f"Erro ao realizar a requisição: {str(e)}")
        return []

# Streamlit UI
st.title("Consulta de Órgãos de Turismo")

if st.button('Consultar'):
    orgaos = obter_dados()
    if orgaos:
        # Converter lista de órgãos em DataFrame do pandas
        df = pd.DataFrame(orgaos)
        
        # Exibir as primeiras linhas do DataFrame
        st.write("Dados Brutos:")
        st.dataframe(df.head())
        
        # Exemplo de filtragem de dados (ex.: órgãos com nome contendo "Turismo")
        df_filtered = df[df['nome'].str.contains("Turismo", case=False)]
        
        # Exibir os dados filtrados
        st.write("Dados Filtrados:")
        st.dataframe(df_filtered)
        
        # Visualização dos dados (ex.: contagem de órgãos por tipo)
        st.write("Visualização dos Dados:")
        plt.figure(figsize=(10, 6))
        sns.countplot(y='nome', data=df_filtered, palette='viridis')
        plt.title('Órgãos com Nome Contendo "Turismo"')
        plt.xlabel('Contagem')
        plt.ylabel('Nome do Órgão')
        st.pyplot(plt)
        
        # Download dos dados filtrados
        csv = df_filtered.to_csv(sep=';', index=False).encode('utf-8')
        st.download_button(
            label="Download dos dados filtrados em CSV",
            data=csv,
            file_name='dados_filtrados.csv',
            mime='text/csv',
        )
    else:
        st.warning("Nenhum dado encontrado. Por favor, tente novamente mais tarde.")
