from googleapiclient.discovery import build
from textblob import TextBlob

# YouTube Data API key
API_KEY = 'YOUR_YOUTUBE_DATA_API_KEY'

# Function to fetch comments from a YouTube video
def get_video_comments(video_id):
    youtube = build('youtube', 'v3', developerKey=API_KEY)
    response = youtube.commentThreads().list(
        part='snippet',
        videoId=video_id,
        textFormat='plainText'
    ).execute()

    comments = []
    for item in response['items']:
        comment = item['snippet']['topLevelComment']['snippet']['textDisplay']
        comments.append(comment)

    return comments

# Function to perform sentiment analysis on comments
def analyze_sentiment(comments):
    positive_count = 0
    negative_count = 0
    neutral_count = 0

    for comment in comments:
        analysis = TextBlob(comment)
        polarity = analysis.sentiment.polarity

        if polarity > 0:
            positive_count += 1
        elif polarity < 0:
            negative_count += 1
        else:
            neutral_count += 1

    total_comments = len(comments)
    positive_percentage = (positive_count / total_comments) * 100
    negative_percentage = (negative_count / total_comments) * 100
    neutral_percentage = (neutral_count / total_comments) * 100

    print("Sentiment Analysis:")
    print("Positive comments: {:.2f}%".format(positive_percentage))
    print("Negative comments: {:.2f}%".format(negative_percentage))
    print("Neutral comments: {:.2f}%".format(neutral_percentage))

# Example usage
if __name__ == "__main__":
    video_id = 'VIDEO_ID_OF_YOUTUBE_VIDEO'
    comments = get_video_comments(video_id)
    analyze_sentiment(comments)
