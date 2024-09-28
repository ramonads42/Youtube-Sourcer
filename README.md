# Youtube-Sourcer


from google.oauth2.credentials import Credentials
from google_auth_oauthlib.flow import InstalledAppFlow
from google.auth.transport.requests import Request
import os.path
from googleapiclient.discovery import build

# Defina os escopos que você precisa
SCOPES = ['https://www.googleapis.com/auth/youtube.readonly']

def authenticate_youtube():
    creds = None
    # Verifica se já existe um token salvo
    if os.path.exists('token.json'):
        creds = Credentials.from_authorized_user_file('token.json', SCOPES)
    # Se não houver token ou ele expirou, reautentica
    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            flow = InstalledAppFlow.from_client_secrets_file('client_secret.json', SCOPES)
            creds = flow.run_local_server(port=0)
        # Salva o token para futuras execuções
        with open('token.json', 'w') as token:
            token.write(creds.to_json())
    return creds

def list_videos_from_playlists(youtube, playlist_ids):
    for playlist_id in playlist_ids:
        print(f"\nPlaylist ID: {playlist_id}")

        # Lista os vídeos na playlist específica
        video_request = youtube.playlistItems().list(part="snippet", playlistId=playlist_id, maxResults=50)

        while video_request:
            video_response = video_request.execute()

            for video in video_response['items']:
                video_title = video['snippet']['title']
                if "game of thrones" in video_title.lower():  # Verifica se o título contém "game of thrones"
                    print(f"  Título do Vídeo: {video_title} (ID: {video['snippet']['resourceId']['videoId']})")

            # Verifica se há mais vídeos na playlist
            video_request = youtube.playlistItems().list_next(video_request, video_response)

def main():
    creds = authenticate_youtube()
    youtube = build('youtube', 'v3', credentials=creds)

    # Lista de IDs das playlists que você forneceu
    specific_playlists = [
        "PLfJJ4lkm8aTBVPqlD_tOVO6ZkzU3VOLRo",
        "PLfJJ4lkm8aTBuDPZk-c2yBgqy9kGhlxgf",
        "PLfJJ4lkm8aTAyxcAuBwSL6wKztBHk12fz",
        "PLfJJ4lkm8aTDMhQVc3KHaSE81czqGQqrc",
        "PLfJJ4lkm8aTAMnXSYk7Xd80uomW3rx0Oq",
        "PLfJJ4lkm8aTB08rPJNg4HFz4t3CJzKNG5",
        "PLfJJ4lkm8aTAsRTXwCMax8pbQc4AGfD01",
        "PLfJJ4lkm8aTAUWVwNRBu5ubnOQfZj3ABA",
        "PLfJJ4lkm8aTDTRXemiZ6JktELl2anXuQp",
        "PLfJJ4lkm8aTDJMuy4_WjyBrkQFLu9uNC9",
        "PLfJJ4lkm8aTA_-pY0rZF69Oj7592Cneez",
        "PLfJJ4lkm8aTD4RlBO1Ch_2R1q1QildK1h",
        "PLfJJ4lkm8aTBhAMGCkbHz4gscHfIDcDJq",
        "PLfJJ4lkm8aTCi6uwIKH4_ZtiuWXiMWWPj",
        "PLfJJ4lkm8aTCmoS8N4Y7GKttNtkREwJ35",
        "PLfJJ4lkm8aTD2jFlAztqAlGeF09w6KLag",
        "PLfJJ4lkm8aTCZ4wOUZNB6c5OlBMX21_j0",
        "PLfJJ4lkm8aTBhsBmrJv0dOxZ9KOcpD1kQ",
        "PLfJJ4lkm8aTCIQyFk-DzGco-xmPcxJcil",
        "PLfJJ4lkm8aTDks-znplpdTpZLD5WIyu82",
        "PLfJJ4lkm8aTBH3tEkdGrXVYuRKEuqeOq5",
        "PLfJJ4lkm8aTDpmIq0vWsRGrnsLZnu9Yro",
        "PLfJJ4lkm8aTA_aOyyTBi1VFInMBcqLl2M",
        "PLfJJ4lkm8aTCX5QE2J7n8aiO2yEVG1izH",
        "PLfJJ4lkm8aTAhpuceWFN5d-Y-s7Q13pm2",
        "PLfJJ4lkm8aTBZA2A_eMayNLdi_Fpmodsn"
    ]

    # Listar vídeos das playlists específicas
    list_videos_from_playlists(youtube, specific_playlists)

if __name__ == '__main__':
    main()
