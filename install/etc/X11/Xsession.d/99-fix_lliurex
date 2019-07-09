#!/usr/bin/env python3

import os
import re

class FixLanguage:
    def __init__(self,home):
        self.path_language='{home}/.config/plasma-locale-settings.sh'.format(home=home)
        self.language_to_fix = ['es','ca@valencia']

    def parse_file(self):
        with open(self.path_language) as fd:
            self.content = fd.readlines()
        regex_language = re.compile("^export LANGUAGE=(.*)")
        search_language = list(filter(regex_language.match,self.content))
        if len(search_language) > 0 :
            list_of_languages = regex_language.search(search_language[0].strip())
            self.languages = list_of_languages.groups()[0].split(':')
        else: 
            self.languages = list()   

    def generate_lang_and_language(self):
        if self.languages[0] == 'es':
            self.lang = 'es_ES.UTF-8'
        elif self.languages[0] == 'ca@valencia':
            self.lang = 'ca_ES.UTF-8@valencia'
            self.languages = ['ca_ES.UTF-8@valencia','ca_ES@valencia'] + self.languages

    def is_need_fix(self):
        return self.languages[0] in self.language_to_fix

    def write_file(self):
        regex_comment = re.compile("^#")
        comments = list(filter(regex_comment.match,self.content))
        if comments[-1].startswith('# This file is modified on session start by lang-fixer'):
            comments = comments[0:-2]
        comments += ['#\n','# This file is modified on session start by lang-fixer script only if first language on language variable are [{languagetofix}]\n'.format(languagetofix='|'.join(self.language_to_fix))]
        with open(self.path_language,'w') as fd:
            fd.writelines(comments)
            fd.write('export LANG={lang}\n'.format(lang=self.lang))
            fd.write('export LANGUAGE={languages}\n'.format(languages=':'.join(self.languages)))

    def run(self):
        if os.path.exists(self.path_language):
            self.parse_file()
            if len(self.languages) > 0 and self.is_need_fix():
                self.generate_lang_and_language()
                self.write_file()
            
            
if __name__ == "__main__":
    app = FixLanguage(os.environ['HOME'])
    app.run()