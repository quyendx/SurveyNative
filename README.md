# SurveyNative
Native surveys built from declarative definitions (JSON). Many question types, skip logic etc.

## Features

  - Displays a single question at a time, but allow the user to scroll up to read and change previous answers.
  - Takes input from a JSON file.
  - Many supported question types, including single selection, multiple selection, single text field, additive text fields, segment choice, year picker, date picker, and table-style questions.
  - Support for sub-questions.
  - Support for showing/hiding questions based on previous answers.

## Example Survey

![Video showing example app](/README/survey_video_720.gif "Survey Video")

## JSON Input

[Example JSON Input file](Example/SurveyNative/ExampleQuestions.json)

The expected input is an array of `questions` and a `submit` object, detailing how to submit the answers.

### Keys used in Questions

#### Some keys are standard across all question types. Others are only present for some question types.

  - `id` (_String_): Required. Used to key answer. Also used to check show/hide conditions.

  - `header` (_String_): Required. May be empty. Displayed as section header.

  - `question` (_String_): Required. Text to display for question.

  - `question_type` (_String_): Required. The chosen type may require additional keys.
    - `single_select` [screenshot](#single_select) | [json](#single_select)
    - `multi_select` [screenshot](#multi_select) | [json](#multi_select)
    - `year_picker` [screenshot](#year_picker) | [json](#year_picker)
    - `date_picker` [screenshot](#date_picker) | [json](#date_picker)
    - `single_text_field` [screenshot](#single_text_field) | [json](#single_text_field)
    - `multi_text_field` [screenshot](#multi_text_field) | [json](#multi_text_field)
    - `dynamic_label_text_field` [screenshot](#dynamic_label_text_field) | [json](#dynamic_label_text_field)
    - `add_text_field` [screenshot](#add_text_field) | [json](#add_text_field)
    - `segment_select` [screenshot](#segment_select) | [json](#segment_select)
    - `table_select` [screenshot](#table_select) | [json](#table_select)

  - `sub_questions` (_Array of questions_): Optional. Expected keys in each question are the same as a top-level question, except that header is not required (or shown if provided). Normally, a sub-question would have a `show_if` key, but it's not required. The `show_if` section of a sub-question may refer to previous sub-question answers.

  - `show_if` (_Conditions_): Optional. If not provided, the question will default to being shown. See the [Structure for Show/Hide question](#structure-for-showhide-question) below for more info.

#### The keys below are specific to certain question types.

  - `options` (_Array of Strings or Other object_): Required for `single_select`, `mult_select`, `table_select` question_types. The `table_select` does not support the Other object. The Other object is a JSON object with a key for `title`. When selected, the user may enter text into a text field.

  - `label` (_String_): Optional for `single_text_field` question type.

  - `label_options` (_Array containing Strings or String Arrays_): Required for `dynamic_label_text_field`.

  - `input_type` (_String_): Optional for `single_text_field`, `dynamic_label_text_field`, `add_text_field` question_types. Can be set to `number` to change the default keyboard to the number keyboard for the text field(s).

  - `values` (_Array of String_): Required for `segment_select` question_type. These are the values the user will choose between.

  - `low_tag` (_String_): Optional for `segment_select` question_type. This is a label for the lowest (first) value.

  - `high_tag` (_String_): Optional for `segment_select` question_type. This is a label for the highest (last) value.

  - `table_questions` (_Array of table questions_): Required for `table_select` question_type. Each table question should have a `title` and an `id` attribute.

### Structure for Show/Hide question

Whether a question is shown or hidden is dependent on the `show_if` key. If the key is missing, the default is to show the question. Both simple conditions and decision trees are supported. The decision trees can contain other decision trees, so you can have fairly complicated logic. There is probably some limit to how far you can nest them.

#### Simple Condition Keys

  - `id` (_String_): Required. This is the id for a question.

  - `subid` (_String_): Optional. This allows access to answers to `table_select` questions to be used, or any other answer that's within a dictionariy.

  - `operation` (_String_): Required. Supported operations:
    - `equals`
    - `not equals`
    - `greater than`
    - `greater than or equal to`
    - `less than`
    - `less than or equal to`

  - `value` (_Any_): Required. This is the value to compare the answer to.

#### Decision Tree Keys

  - `operation` (_String_): Required. Can be `or` or `and`. If you need a combination, you should be able to use nesting to get it.

  - `subconditions` (_Array of Simple Conditions or Decision Trees_): Required.

### Submit

The submit object (a peer to `questions`) requires only two keys, `button_title` and `url`. Both are required strings.

### Question Type Examples

#### single_select

```
{
  "id": "ice_cream",
  "header": "Question 1",
  "question": "What is your favorite ice cream flavor?",
  "question_type": "single_select",
  "options": [
    "Strawberry",
    "Chocolate",
    "Vanilla",
    {
      "title": "Other",
      "type": "freeform"
    }
  ]
}
```

#### multi_select

```
{
  "id": "music_types",
  "header": "Question 6",
  "question": "What types of music do you like?",
  "question_type": "multi_select",
  "options": [
    "Pop",
    "Rock",
    "Rap",
    "Country",
    {
      "title": "Other",
      "type": "freeform"
    }
  ]
}
```

#### year_picker

```
{
  "id": "birthyear",
  "header": "Question 2",
  "question": "Enter the year of your birth.",
  "question_type": "year_picker"
}
```

#### date_picker

```
{
  "id": "last_antibiotic_date",
  "question": "On what date did you take the final dose of your antibiotics:",
  "question_type": "date_picker"
}
```

#### single_text_field

```
{
  "id": "age",
  "header": "Question 4",
  "question": "What is your current age in years?",
  "question_type": "single_text_field",
  "label": "Years",
  "input_type": "number"
}
```

#### multi_text_field



#### dynamic_label_text_field

```
{
  "id": "height",
  "header": "Question 7",
  "question": "How tall are you?",
  "question_type": "dynamic_label_text_field",
  "label_options": [
    [
      "Feet",
      "Inches"
    ],
    "Centimeters"
  ],
  "input_type": "number"
}
```

#### add_text_field

```
{
  "id": "which_sports",
  "question": "Which sports do you like to play?",
  "question_type": "add_text_field",
  "input_type": "default"
}
```

#### segment_select

```
{
  "id": "happiness",
  "header": "Question 8",
  "question": "How happy are you?",
  "question_type": "segment_select",
  "low_tag": "Not happy",
  "high_tag": "Super happy",
  "values": [
    "1",
    "2",
    "3",
    "4",
    "5",
    "6",
    "7"
  ]
}
```

#### table_select

```
{
  "id": "weekend_activities",
  "header": "Question 9",
  "question": "On the weekends, do you:",
  "question_type": "table_select",
  "options": [
    "Yes",
    "Sometimes",
    "No"
  ],
  "table_questions": [
    {
      "title": "Play sports?",
      "id": "play_sports"
    },
    {
      "title": "Read books?",
      "id": "read_books"
    },
    {
      "title": "Go dancing",
      "id": "go_dancing"
    },
    {
      "title": "Watch TV and movies?",
      "id": "watch_tv"
    }
  ]
}
```

### Contributing

Areas we'd love to see contributions:

1. Bug fixes
2. New question types
3. Customizable styling
4. Customizable animation
5. Dynamic question number substitution in header
5. Option to enable/disable animations
6. Android Port
7. Export qualtrics/survey-monkey surveys to SurveyNative
8. CareKit/ResearchKit integration
