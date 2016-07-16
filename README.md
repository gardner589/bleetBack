# Blitter

## Local Setup

```

git clone https://github.com/ga-wdi-exercises/blitter-api
cd blitter-api
bundle install
rake db:create db:migrate db:seed
rails s
# open http://localhost:3000/bleets.json
```
#Example Code for the Grumblr seeds

```
require('buzzfeed_title_generator')
require('httparty')

Comment.delete_all
Grumble.delete_all

names = %w(Jesse Adam Andy Robin Adrian Matt)

def advice
  JSON.parse(HTTParty.get("http://api.adviceslip.com/advice").body)["slip"]["advice"]
end

def photo
  JSON.parse(HTTParty.get("http://www.splashbase.co/api/v1/images/random?images_only=true").body)["url"]
end

def comment
  comments = [
    "That photo reminds me of the time I saw",
    "Your post would be better if it included",
    "There is no God. There is only",
    "I disagree. I'd encourage you to think more broadly of",
    "This just goes to show your ignorance about",
    "Who wrote this? It sounds like it was written by",
    "This is all well and good, but one mustn't forget about",
    "Hey, OP, people said the same thing when there was",
    "I'm not sure how to feel about this. My opinions are usually informed by",
    "I feel like a more appropriate picture for this post would be",
    "It seems like all you see on the news these days is",
    "Ugh. Modern society is so obsessed with",
    "I've responded to this in my post about",
    "Didn't you know? The government is secretly controlled by"
  ]
  message = HTTParty.get("http://www.ineedaprompt.com/api").parsed_response["english"]
  puts message
  comments.sample + " " + message[0,1].downcase + message[1..-1]
end

names.each do |name|
  current_grumble = Grumble.create!(
    authorName: name,
    title: BuzzfeedTitleGenerator.make_title,
    content: advice,
    photoUrl: photo
  )
  (rand(4) + 2).times do
    current_grumble.comments.create!(authorName: names.sample, content: comment)
  end
end

```

## Buzzfeed Title Generator

```

module BuzzfeedTitleGenerator
  NUMBERS = (8..33).to_a

  ADJECTIVES = %w( awesome rediculous absurd sweet suite gnarly random quirky
                   hipster shocking alarming over-rated under-rated
                   zombie-proof )

  PLAIN_NOUNS =  ['kanye quotes', 'trading cards', 'pokemons', 'T-shirts',
                  'tornadoes', 'sneezes', 'GIFs', 'hipchat messages',
                  'cheeses', 'security vulnerabilities', 'selfies',
                  'umbrellas', 'Pixar movies', 'Justin Bieber quotes',
                  'Justice Beaver quotes']

  ACTION_NOUNS = ['tigers', 'nerds', 'grizzly bears', 'pokemons',
                  'BurgerKing employees', 'TV moms', 'TV dads',
                  'scientologists', 'WDI instructors', 'WDI students',
                  'photos of communists', 'trends', 'skateboard tricks',
                  'owls', 'guinea pigs', 'panda bears', 'chickens', 'penguins',
                  'things you didn\'t know about',
                  'things you should know about']

  ACTIVITIES =   ['eating sandwiches', 'wearing shoes',
                  'taking pictures with the queen', 'dressing as elephants',
                  'taking selfies', 'wearing sweatpants', 'wearing a tux',
                  'going to the gym', 'blogging for fun and profit',
                  'writing rails apps']

  FLOURISHES =   ['ever', 'of all time', 'that will blow your mind',
                  'that programmers hate',
                  'that will make you want to move to DC',
                  'that will make you want to leave DC',
                  'in the entire universe', 'that were better in the 90\'s',
                  'that were better in the 80\'s',
                  'that people born after 1990 wil never understand',
                  'that will make you want to wake up earlier every morning',
                  'that will make you want to call your parents']

  def self.make_title
    number = NUMBERS.sample

    if rand(100) < 50
      noun1 = PLAIN_NOUNS.sample
      noun2 = (PLAIN_NOUNS.reject { |e| e == noun1 }).sample
      title = "#{number} #{ADJECTIVES.sample} #{noun1}"
      maybe(25) { title = "#{title} and #{noun2}" }
    else
      title = "#{number} #{ACTION_NOUNS.sample} #{ACTIVITIES.sample}"
    end

    maybe(5) { title = "The #{title}" }
    maybe(25) { title = "#{title} #{FLOURISHES.sample}" }

    title
  end

  def self.maybe(odds = 50)
    yield if rand(100) < odds
  end
end

```
