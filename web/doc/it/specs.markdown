---
layout: doc_it
title: Specifiche
previous: Ruby - Variabili globali
previous_url: ruby/global-variables
next: RubySpec
next_url: specs/rubyspec
---

Lo sviluppo del progetto Rubinius è guidato generalmente da specifiche
eseguibili in stile TDD/BDD. La cartella 'spec' si divide concettualmente
i due parti:

  1. I file in './spec/ruby' descrivono il comportamento dell'interprete
     Ruby ufficiale.
  2. I restanti file nella cartella './spec' descrivono invece il
     comportamento di Rubinius.

Le specifiche in ./spec/ruby sono una copia delle RubySpec ad una particolare
revisione. Queste vengono importate regolarmente dal progetto RubySpec e le
specifiche che falliscono vengono taggate per far sì che il processo di
Continuous Integration esegua sempre un set di specifiche riconosciute come
funzionanti. Ciò consente di assicurarsi facilmente che cambiamenti al codice
di Rubinius non causino regressioni.

Il sito del [progetto RubySpec](http://rubyspec.org/) contiene documentazione
sull'organizzazione delle specifiche e linee guide per scriverle.

Nell'aggiungere codice e specifiche a Rubinius utilizzate la seguente
procedura:

  1. Scrivete una specifica che fallisce riguardo a qualche aspetto del
     comportamento di Ruby. Effettuate un commit separato con i file necessari
     in ./spec/ruby.
  2. Aggiungete a Rubinius il codice necessario a far passare le specifiche.
     Effettuate un commit separato da quello con i cambiamenti alle
     specifiche.
  3. Lanciate il comando `rake` per assicurarvi che tutte le specifiche
     eseguite nel processo di Continuous Integration passino.

I cambiamenti ai file in ./spec/ruby vengono regolarmente integrati nel
progetto RubySpec. Allo stesso modo, i cambiamenti correnti effettuati alle
RubySpec da collaboratori di altre implementazioni Ruby vengono regolarmente
integrati in ./spec/ruby. In questo caso vengono aggiornati anche i tag della
Continuous Integration.
